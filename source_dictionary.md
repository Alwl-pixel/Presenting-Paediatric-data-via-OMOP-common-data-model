# Source Data Dictionary (Synthetic) — Paediatric Vitals + PEWS

## Purpose
This document defines the *source (pre-OMOP)* fields required to represent paediatric vital signs and PEWS in the OMOP Common Data Model.
It is designed for a synthetic prototype and does not use real patient data.

## Assumptions
- Vitals are recorded on a ward observation chart (electronic or paper-equivalent).
- Measurements are timestamped at the time of observation.
- PEWS is recorded or can be derived from component observations.
- Units may vary by system and require standardisation during ETL.

## Data quality expectations
- Each observation should have: patient identifier (synthetic), visit identifier (synthetic), datetime, and observer type (e.g., nurse).
- Numeric values must have an explicit unit or a documented default unit.
- Categorical fields must use a controlled list of allowed values.

- ## Source fields

| Source field (local name) | Clinical meaning | Data type | Allowed values / Range | Unit (if numeric) | Typical frequency | Who records | Common issues / notes |
|---|---|---|---|---|---|---|---|
| obs_datetime | Date/time observation taken | datetime | ISO 8601 | n/a | q1–6h | nurse/HCA | Timezone/daylight saving; back-charting |
| hr | Heart rate | integer | 40–240 (age-dependent) | beats/min | q1–6h | nurse/HCA/monitor | Monitor vs manual; artefact in crying/fever |
| rr | Respiratory rate | integer | 10–100 (age-dependent) | breaths/min | q1–6h | nurse/HCA | Counting quality varies; distress inflates |
| temp | Temperature | decimal | 34.0–42.5 | °C | q4–6h | nurse/HCA | Device type (tympanic/axillary); rounding |
| spo2 | Oxygen saturation | integer | 50–100 | % | q1–6h | nurse/HCA/monitor | Motion artefact; probe issues; record on air vs O2 |
| sbp | Systolic blood pressure | integer | 40–180 (age-dependent) | mmHg | q4–12h | nurse/HCA/monitor | Often missing in small children; cuff size |
| dbp | Diastolic blood pressure | integer | 20–120 (age-dependent) | mmHg | q4–12h | nurse/HCA/monitor | Same as above |
| o2_device | Oxygen delivery device | categorical | room_air, nasal_cannula, simple_mask, non_rebreather, venturi, humidified_high_flow, nippv, invasive_vent | n/a | q1–6h | nurse | Terminology varies across wards; map to standard list |
| o2_flow_rate | Oxygen flow rate | decimal | 0.0–15.0 | L/min | q1–6h | nurse | Missing when FiO2 used; device-specific |
| avpu | Conscious level (AVPU) | categorical | A, V, P, U | n/a | q1–6h | nurse | Sometimes replaced by GCS or “Alert” text |
| crt | Capillary refill time | categorical | normal, prolonged | n/a | q4–6h | nurse/clinician | Recorded inconsistently; “<2s” vs “>2s” |
| pews_total | Paediatric Early Warning Score total | integer | 0–20 (local policy) | score | q1–6h | nurse | PEWS definition varies by Trust; must document chart logic |
| clinician_concern | Staff/parental concern flag (if used) | categorical | none, staff_concern, parent_concern, both | n/a | as needed | nurse/clinician | Often free text; create controlled values for prototype |


## PEWS definition note
PEWS scoring systems vary between NHS organisations (age bands, thresholds, and components differ).
For this synthetic prototype:
- `pews_total` is treated as a recorded field OR derived using an illustrative local scoring rule documented in `pews_definition.md`.
- This project is not intended for clinical use or deployment.


## Record linkage keys (synthetic)
Each observation row is expected to include:
- `person_source_id` (synthetic patient identifier)
- `visit_source_id` (synthetic encounter/admission identifier)
- `obs_datetime`

## Paediatric-specific considerations
- Normal physiological ranges vary substantially by age; plausibility checks must be age-banded.
- Many measurements are intermittently missing (e.g., BP in infants, FiO2 on ward).
- Vitals may be recorded from monitors, manually observed, or transcribed—source provenance affects reliability.
