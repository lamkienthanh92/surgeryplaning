# Methods and validation

## 1. Template knee

- **Source:** cadaver specimen S193761 (Andreassen et al., Dryad, CC0). MRI segmentations of femur, tibia/fibula, patella, femoral/tibial/patellar cartilage, ACL, PCL, MCL and LCL.
- **Processing:** left knee mirrored to a right knee; frame with x lateral, y anterior, z proximal, origin at the tibial plateau centre (joint line z = 0). Meshes decimated for the browser; ambient occlusion baked from the geometry.
- **Landmarks:** lateral/medial epicondyles = most prominent points of each condyle; transepicondylar axis = flexion axis. Gerdy's tubercle = most anterolateral point of the proximal tibia 5–25 mm below the joint line; fibular-head apex = most proximal fibular point. Ligament attachments = ends of each ligament's principal axis.
- **Colour scans** (femur, patella, tibia with menisci) registered to the MRI model by multi-start trimmed ICP (median surface distance ≈1.5 mm for femur and patella, ≈4 mm for the tibia because of potting and soft tissue). Display only.

## 2. Kinematics

- **Default:** passive flexion path of the same knee recorded on a 6-DOF robot (Dynamics Data, PFLEX, mean of 3 trials): tibial internal rotation (≈4° by 70°) and anterior/medial–lateral translation (≈8 mm anterior by 70°). Grood–Suntay translations are applied in the tibial (floating-axis) frame; applying them in the femoral frame produced 7.5 mm cartilage penetration at 70°, which was rejected. Data cover 0–70°; held constant beyond.
- **Alternative:** coupled rotation proportional to flexion, fitted to Helito 2014 (kept for comparison; larger than this knee actually rotates).
- **Patella:** 0.7° per degree of knee flexion about the transepicondylar axis, then translated to restore the MRI cartilage contact. Orientation only.

## 3. Literature check of the geometry (default kinematics, no fitted parameter)

| Study | Expected | Model | Result |
|---|---|---|---|
| Helito 2014 | epicondylar ALL +16.7% (0→90°) | +8.7% | right direction, half the size |
| Zens 2015 | +0.15%/° | +0.10%/° | right direction, smaller |
| Zens 2015 | +33.8 ± 9.6% at 90° + 25° IR | 27–41% | agrees |
| Imbert 2016 | proximal-posterior origin shortens; others lengthen | −15.5 / +8.7 / +30.9% | agrees |
| Dodds 2014 | near-isometric 0–60° | −0.6% | agrees |
| Kittl 2015 | LET deep to LCL has smaller strain range | 10.2% vs 7.3% | disagrees |

## 4. Rotational "virtual knee" model (Teaching tab, risk simulator)

Lumped-parameter model: knee rotational stiffness K = T/θ with each structure carrying a fraction of the intact stiffness (ACL, ALL, Kaplan fibres/ITB). Fractions are derived from robotic sectioning data (Geeslin 2018; Lee 2023 meta-analysis for the ALL at 30–90°). Between-knee variation, between-laboratory heterogeneity (h) and graft efficacy (c for ALLR, m·c for LET; Geeslin 2017, Noyes 2017, Delaloye 2020) are sampled by Monte Carlo. Leave-one-study-out validation: 4/5 points within the 95% prediction interval. Outcome thresholds: under-constrained > +2°, over-constrained < −2° vs intact.

## 5. Vietnamese reference anatomy

Aggregate values from 22 formalin-fixed Vietnamese knees (caliper measurements): ALL origin 12.7 ± 3.5 mm from the lateral epicondyle (direction not recorded), insertion at 50% of the Gerdy–fibular-head distance, ALL widths 7.5 / 5.5 / 7.5 mm (femur / joint line / tibia), thickness 0.77 mm, footprints 59 / 62 mm², ITB width 25.6 mm, ITB footprint on Gerdy 266 mm². Flagged entry errors were excluded. Repeat readings (three per measurement) gave ICC 0.91–0.99, SEM 0.9° and MDC95 2.5° for internal rotation. Because the origin direction was not recorded, the planning preset uses the most isometric point on the measured-distance ring.

## 6. Patient scan module

- **DICOM reading:** in-browser parser (Explicit/Implicit VR Little Endian), series grouped by Series Instance UID, slices sorted along the slice normal, spacing from slice positions, frame of reference checked so landmarks are never mixed between sessions.
- **Bone extraction (CT):** resampling to ≈0.7–0.9 mm, threshold (default 250 HU, adjustable), flood fill of the background from the volume borders to fill marrow cavities, two 6-neighbour erosions to split thin contacts, connected-component labelling, label regrowth into the original mask, 3×3×3 smoothing and surface nets.
- **Labelling and landmarks:** femur and tibia = two largest components (by height); fibula = elongated component below the joint line; patella = anterior component near the distal femur; components < 4 cm³ = possible fragments. Side from the fibula's position. Landmarks found automatically as for the template, then fitted rigidly (no scaling) to the model frame with mirroring for left knees.
- **Synthetic test** (`samples/synthetic_knee_CT.zip`, 0.7 × 0.7 × 1.0 mm): five parts found in ≈8 s (femur, tibia, fibula, patella, 0.1 cm³ fragment); side detected as left; epicondylar width 90.9 mm (true 90.7) and Gerdy–fibular head 52.1 mm (true 51.6).
- **Tibial slope:** angle between the anterior–posterior rim line of the lateral plateau and the plane perpendicular to the proximal tibial axis (two shaft centres), from user-placed points.

## 7. Planning extras

- **Tunnel convergence:** ACL femoral tunnel 9 mm from the femoral footprint to the lateral cortex (exit adjustable); ALL socket 5 × 25 mm from the femoral fixation point with adjustable anterior/proximal aim. Reported as wall-to-wall distance at the cortex and inside the bone.
- **Indication flags:** STABILITY trial inclusion profile (Getgood 2020: age ≤25 with ≥2 of pivot shift ≥2, return to pivoting sport, Beighton ≥4) and expert-consensus factors (Sonnery-Cottet 2017; Getgood 2019). Tibial slope >12° flagged as a commonly cited threshold. Flags restate sources and do not make decisions.
