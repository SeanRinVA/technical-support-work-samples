# Post-Remediation Runbook: Backup Restore Validation

## Purpose

Provide a repeatable process for validating backup recoverability after backup configuration changes.

## When to Use

Use this runbook after:

- Backup client configuration changes
- Policy or schedule changes
- Retention changes
- Include/exclude rule changes
- Operating system upgrades
- Storage layout changes
- Failed or questionable restore attempts

## Validation Steps

### 1. Confirm Backup Scope

Verify:

- Host/client name
- File systems or paths included
- Exclusions
- Policy/domain assignment
- Schedule
- Retention behavior

### 2. Run Backup

Start or monitor the next scheduled backup.

Confirm:

- Job completed
- No critical warnings
- Expected paths were included
- Backup duration is reasonable
- Backup size aligns with expectations

### 3. Select Restore Test Target

Choose a non-production restore location.

Do not overwrite production data during validation.

### 4. Perform Test Restore

Restore a representative sample of files.

Confirm:

- Files restore successfully
- Permissions are acceptable
- File timestamps are expected
- Restored data matches the requested path or data set

### 5. Document Results

Record:

- Date/time of validation
- Source system
- Backup policy
- Restore point used
- Files or paths tested
- Result
- Issues found
- Corrective actions taken

## Success Criteria

Backup recoverability is considered validated when:

- Backup completes without critical errors.
- Expected data is visible in the restore catalog.
- Test restore completes successfully.
- Restored data is usable.
- Procedure is documented.

## Cautions

- Do not assume backup completion equals restore readiness.
- Do not test restores directly over production data.
- Revalidate after configuration or policy changes.
- Preserve notes from failed restore attempts; they often identify documentation or configuration gaps.
