# ZarishSphere Code Standards

> **Version:** 2.0.0 | **Updated:** 2026-03-24
> These standards apply to all ZarishSphere repositories. CI enforces them automatically.

---

## 1. Go (1.26.1) Standards

### 1.1 Required Libraries — Use Exactly These

```go
// HTTP routing
github.com/go-chi/chi/v5              // NEVER: gin, echo, fiber

// PostgreSQL
github.com/jackc/pgx/v5               // NEVER: database/sql alone, gorm

// Logging
github.com/rs/zerolog                 // NEVER: fmt.Println, log.Print, logrus

// Config
github.com/spf13/viper                // NEVER: os.Getenv directly

// Auth
github.com/coreos/go-oidc/v3/oidc    // SMART on FHIR
github.com/golang-jwt/jwt/v5          // JWT parsing

// Messaging
github.com/nats-io/nats.go           // NATS JetStream 2.12.5

// Cache
github.com/valkey-io/valkey-go       // Valkey 9.0.3

// Telemetry
go.opentelemetry.io/otel             // OpenTelemetry 1.35+
github.com/prometheus/client_golang   // Prometheus metrics

// Testing
github.com/testcontainers/testcontainers-go  // Integration tests
github.com/stretchr/testify/assert           // Assertions

// Migrations
github.com/golang-migrate/migrate/v4  // SQL migrations
```

### 1.2 Error Handling

```go
// ✅ Correct — always wrap errors with context
func getPatient(ctx context.Context, id string) (*Patient, error) {
    patient, err := repo.FindByID(ctx, id)
    if err != nil {
        return nil, fmt.Errorf("getPatient %s: %w", id, err)
    }
    return patient, nil
}

// ❌ Wrong — bare error return with no context
func getPatient(ctx context.Context, id string) (*Patient, error) {
    patient, err := repo.FindByID(ctx, id)
    if err != nil {
        return nil, err  // No context!
    }
    return patient, nil
}

// ✅ Check error type
if errors.Is(err, ErrNotFound) {
    return http.StatusNotFound, nil
}

// ❌ Never panic in production
panic("this should not happen")  // Forbidden
```

### 1.3 Logging

```go
// ✅ Correct — structured zerolog
log := zerolog.Ctx(ctx)
log.Info().
    Str("patient_id", patientID).
    Str("tenant_id", tenantID).
    Int("duration_ms", duration).
    Msg("patient read completed")

// ❌ Wrong — unstructured logging
fmt.Println("patient read completed")  // Forbidden
log.Printf("patient %s read", patientID)  // Forbidden
```

### 1.4 Database Queries

```go
// ✅ Correct — parameterized, tenant-scoped
rows, err := pool.Query(ctx,
    `SELECT resource FROM fhir.resources
     WHERE resource_type = $1
     AND fhir_id = $2
     AND tenant_id = $3
     AND deleted_at IS NULL`,
    resourceType, fhirID, tenantID)

// ❌ Wrong — string concatenation (SQL injection risk)
query := "SELECT resource FROM fhir.resources WHERE fhir_id = '" + fhirID + "'"

// ❌ Wrong — missing tenant_id (data leak across tenants)
rows, err := pool.Query(ctx,
    "SELECT resource FROM fhir.resources WHERE fhir_id = $1",
    fhirID)
```

### 1.5 HTTP Handlers

```go
// ✅ Correct handler pattern
func (h *PatientHandler) Create(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()
    log := zerolog.Ctx(ctx)

    // 1. Extract tenant from JWT (via middleware)
    tenantID := auth.TenantFromContext(ctx)

    // 2. Validate SMART scope
    if !auth.HasScope(ctx, "patient/*.write") {
        http.Error(w, "Forbidden", http.StatusForbidden)
        return
    }

    // 3. Decode and validate request
    var patient fhir.Patient
    if err := json.NewDecoder(r.Body).Decode(&patient); err != nil {
        http.Error(w, "Bad Request", http.StatusBadRequest)
        return
    }

    // 4. Execute with trace span
    ctx, span := otel.Tracer("patient-svc").Start(ctx, "patient.create")
    defer span.End()

    // 5. Call service layer
    created, err := h.svc.Create(ctx, tenantID, &patient)
    if err != nil {
        log.Error().Err(err).Msg("failed to create patient")
        http.Error(w, "Internal Server Error", http.StatusInternalServerError)
        return
    }

    // 6. Write FHIR AuditEvent (async)
    go h.audit.LogCreate(context.Background(), "Patient", created.Id, tenantID)

    // 7. Publish NATS event (async)
    go h.events.Publish("zs.fhir.Patient.created", created)

    // 8. Return response
    w.Header().Set("Content-Type", "application/fhir+json")
    w.WriteHeader(http.StatusCreated)
    json.NewEncoder(w).Encode(created)
}
```

---

## 2. TypeScript (6.0) / React (19.x) Standards

### 2.1 Component Pattern

```tsx
// ✅ Correct — functional component, typed props, no any
interface PatientBannerProps {
  patientId: string;
  tenantId: string;
}

export function PatientBanner({ patientId, tenantId }: PatientBannerProps) {
  const { data: patient, isLoading, error } = usePatient(patientId, tenantId);

  if (isLoading) return <PatientBannerSkeleton />;
  if (error) return <ErrorAlert message={error.message} />;
  if (!patient) return null;

  return (
    <div className="patient-banner">
      <h1>{patient.name?.[0]?.text}</h1>
      <p>{formatDate(patient.birthDate)}</p>
    </div>
  );
}

// ❌ Wrong — class component, no types
class PatientBanner extends React.Component {
  render() {
    return <div>{this.props.patient.name}</div>;
  }
}
```

### 2.2 FHIR Data Fetching

```tsx
// ✅ Use zs-pkg-ui-fhir-hooks
import { usePatient, useFHIRMutation } from '@zarishsphere/zs-pkg-ui-fhir-hooks';

function PatientProfile({ patientId }: { patientId: string }) {
  const { data, isLoading } = usePatient(patientId);
  // ...
}

// ❌ Don't fetch FHIR directly in components
function PatientProfile({ patientId }: { patientId: string }) {
  const [patient, setPatient] = useState(null);
  useEffect(() => {
    fetch(`/fhir/R5/Patient/${patientId}`)
      .then(r => r.json())
      .then(setPatient);
  }, []);
  // ...
}
```

---

## 3. Clinical Form (JSON Schema) Standards

Every JSON Schema clinical form must follow ZS Form Schema v1:

```json
{
  "$schema": "https://zarishsphere.com/schema/form/v1",
  "id": "zs-form-{domain}-{number}",
  "title": "{{i18n:forms.{domain}.title}}",
  "version": "1.0.0",
  "fhirResource": "Observation",
  "sections": [
    {
      "id": "section-1",
      "title": "{{i18n:forms.{domain}.section_1_title}}",
      "fields": [
        {
          "id": "field-001",
          "type": "number",
          "label": "{{i18n:forms.{domain}.field_001_label}}",
          "fhirPath": "Observation.valueQuantity.value",
          "loincCode": "8310-5",
          "unit": "Cel",
          "required": true,
          "validation": {
            "min": 30,
            "max": 45
          }
        }
      ]
    }
  ]
}
```

**Mandatory fields for every form field:**
- `id` — unique within the form
- `type` — one of: `text`, `number`, `date`, `select`, `multiselect`, `boolean`, `textarea`
- `label` — MUST be an i18n key, NEVER inline text
- `fhirPath` — FHIR R5 path where this field maps
- `required` — explicit true or false

**Mandatory for coded fields:**
- `loincCode` (preferred for observations and labs)
- `snomedCode` (for diagnoses and procedures)
- `icd11Code` (for ICD-11 coded fields)

---

## 4. File Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| Go files | `snake_case.go` | `patient_handler.go` |
| Go test files | `snake_case_test.go` | `patient_handler_test.go` |
| TypeScript files | `PascalCase.tsx` for components | `PatientBanner.tsx` |
| TypeScript utilities | `camelCase.ts` | `formatDate.ts` |
| JSON Schema forms | `kebab-case.json` | `phq-9-assessment.json` |
| SQL migrations | `NNN_description.sql` | `001_create_patients.sql` |
| Markdown docs | `UPPER-KEBAB-CASE.md` | `HIPAA-CONTROLS.md` |
| YAML configs | `kebab-case.yaml` | `prometheus-rules.yaml` |

---

## 5. Security Rules (All Languages)

These rules are enforced by GitGuardian, CodeQL, and Trivy on every PR:

1. **No secrets in code** — no API keys, passwords, tokens, certificates
2. **No hardcoded URLs** — use environment variables or config files
3. **No `.env` files committed** — use `.env.example` with placeholder values
4. **No PHI in tests** — use synthetic data from `zs-pkg-go-testing`
5. **No `//nolint` or `// nosec` without a comment** explaining why
6. **No `import _ "unsafe"`** without explicit ADR approval

Violation of these rules will block your PR from merging.

---

*These standards are enforced automatically in CI. Questions → open an issue in zs-docs-platform.*
