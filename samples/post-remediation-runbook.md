# Post-Remediation Runbook: Backup Media Coordination and Restore Validation

## Purpose

Provide a repeatable process for validating backup recoverability after backup infrastructure, TSM server, or tape media coordination changes.

## When to Use

Use this runbook after:

- TSM server role changes
- Tape library configuration changes
- Media pool or media ownership changes
- Backup server rebuilds
- Policy or schedule changes
- Retention changes
- Operating system upgrades
- Storage layout changes
- Failed or questionable restore attempts

## Validation Steps

### 1. Confirm Backup Architecture

Verify:

- Source systems being protected
- TSM servers pulling data from source systems
- TSM servers writing backup data to tape operations
- Controlling/logging server role for media access
- Tape library access path
- Media pool assignment
- Media ownership and reuse rules
- Retention behavior

### 2. Confirm Media Coordination

Confirm:

- Backup servers are not independently overwriting shared media.
- Tape access is coordinated through the defined control/logging point.
- Media use is recorded in a way that can be reviewed later.
- Media ownership aligns with the intended architecture.
- No server is writing outside the expected coordination model.

### 3. Run Backup

Start or monitor the next scheduled backup.

Confirm:

- Job completed
- No critical warnings
- Expected source systems were included
- Backup duration is reasonable
- Backup size aligns with expectations
- Tape media activity matches the intended control model

### 4. Select Restore Test Target

Choose a non-production restore location.

Do not overwrite production data during validation.

### 5. Perform Test Restore

Restore a representative sample of files.

Confirm:

- Files restore successfully
- Permissions are acceptable
- File timestamps are expected
- Restored data matches the requested path or data set
- Restore media matches expected backup records

### 6. Document Results

Record:

- Date/time of validation
- Source system
- TSM server involved
- Controlling/logging server involved
- Tape media used
- Restore point used
- Files or paths tested
- Result
- Issues found
- Corrective actions taken

## Success Criteria

Backup recoverability is considered validated when:

- Backup completes without critical errors.
- Media access follows the controlled/logged architecture.
- Expected data is visible in the restore catalog.
- Test restore completes successfully.
- Restored data is usable.
- Procedure is documented.

## Cautions

- Do not assume backup completion equals restore readiness.
- Do not assume multiple backup servers can safely share tape media without explicit coordination.
- Do not test restores directly over production data.
- Revalidate after server, tape library, media pool, or policy changes.
- Preserve notes from failed restore attempts; they often identify documentation, architecture, or coordination gaps.
