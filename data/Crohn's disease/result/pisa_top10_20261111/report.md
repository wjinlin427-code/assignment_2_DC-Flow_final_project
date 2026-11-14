# PISA top-10 complex analysis

- Service: [PDBe PISA Next](https://wwwdev.ebi.ac.uk/pdbe/pisa/), API host `wwwdev.ebi.ac.uk`.
- Scope: 30 generated full-complex PDBs (top 10 for 6UIB, 8UUI, 9P95).
- Mode: `asis=true`, `ligand_position=auto`; analyzed provided coordinates without crystal-complex prediction.
- All 30 jobs were accepted (HTTP 202) and returned `status=Ok` with interface and complex results.
- PISA values are model-based interface estimates, not experimental affinity measurements or validation of binding in vivo.
- `int_area` is the difference in accessible surface area of separated and interfacing partners divided by two (A²); `int_solv_energy` is solvation free-energy gain upon interface formation (kcal/mol). More negative is more favorable within this model. PISA p-value is the probability of observing a lower ΔiG for randomly picked surface atoms; lower is more unusual. CSS is 0 for all reported interfaces in as-is mode and is not interpreted here.
- For each candidate, peptide-chain `P` interfaces were isolated. Areas and energies below are sums across P–receptor chain interfaces; this is a descriptive aggregate, not a calibrated score.

## Per-target overview

| Target | Mean peptide-interface area (Å²) | Range (Å²) | Mean summed ΔiG (kcal/mol) | Best area candidate | Most negative ΔiG candidate |
|---|---:|---:|---:|---|---|
| 6UIB | 559.4 | 414.5–680.2 | -9.05 | rank 07 (rank_07_run_more_candidate_009.pdb): 680.2 | rank 07 (rank_07_run_more_candidate_009.pdb): -13.3 |
| 8UUI | 435.4 | 285.1–508.7 | -8.63 | rank 09 (rank_09_run_more_candidate_007.pdb): 508.7 | rank 09 (rank_09_run_more_candidate_007.pdb): -10.2 |
| 9P95 | 215.5 | 164.7–256.5 | -2.39 | rank 02 (rank_02_run_candidate_005.pdb): 256.5 | rank 03 (rank_03_run_candidate_009.pdb): -4.6 |

## Candidate-level interface results

| Target | Rank | Sequence | P–target pairs | Area sum (Å²) | ΔiG sum (kcal/mol) | PISA p-value range |
|---|---:|---|---|---:|---:|---:|
| 6UIB | 01 | APSAMDTGTQNAYAG | B-P; A-P | 550.7 | -10.0 | 0.626–0.698 |
| 6UIB | 02 | TDGADNWSWADGFNS | B-P; A-P | 594.2 | -7.0 | 0.590–0.849 |
| 6UIB | 03 | PGHATASNGTGFFPD | B-P; A-P | 530.5 | -7.7 | 0.649–0.865 |
| 6UIB | 04 | QQVQVPDGTWAGWVV | B-P; A-P | 595.7 | -9.3 | 0.631–0.674 |
| 6UIB | 05 | PQVDPDGNGGGSPST | B-P; A-P | 495.4 | -6.5 | 0.526–0.835 |
| 6UIB | 06 | APYHATQTPWDSADG | B-P; A-P | 633.5 | -9.1 | 0.584–0.704 |
| 6UIB | 07 | VYYWHGCWPGAALMA | B-P; A-P | 680.2 | -13.3 | 0.699–0.778 |
| 6UIB | 08 | WQNTGDAGSDDIHPD | A-P; B-P | 493.3 | -8.0 | 0.504–0.532 |
| 6UIB | 09 | QAVADTGVGGHWITP | B-P; A-P | 605.8 | -9.9 | 0.565–0.779 |
| 6UIB | 10 | PAGSADTHSAGSHAA | A-P; B-P | 414.5 | -9.7 | 0.479–0.661 |
| 8UUI | 01 | VDMSFSNTNGVDNSAFNT | B-P | 459.7 | -9.3 | 0.443–0.443 |
| 8UUI | 02 | NDYWPHGHDGAMDPSATT | B-P; A-P | 454.8 | -7.1 | 0.346–0.688 |
| 8UUI | 03 | QNCHGAPGNSSDSAGMND | B-P; A-P | 372.7 | -8.2 | 0.321–0.517 |
| 8UUI | 04 | DYGNAVSFSFPAFGGAAT | B-P | 455.3 | -8.6 | 0.672–0.672 |
| 8UUI | 05 | PMYMPANHGDGGAGFSGN | B-P | 285.1 | -5.1 | 0.469–0.469 |
| 8UUI | 06 | AGADGWMDNGDGAPYWTG | B-P | 472.2 | -9.1 | 0.668–0.668 |
| 8UUI | 07 | SSIPWYSEHVIGWSSADE | B-P | 406.0 | -10.0 | 0.438–0.438 |
| 8UUI | 08 | PWCTAVNGNVSTDDTDPA | B-P | 476.9 | -9.4 | 0.533–0.533 |
| 8UUI | 09 | GTHTMGVYCAITWIHHVD | B-P | 508.7 | -10.2 | 0.665–0.665 |
| 8UUI | 10 | ATAQPRTAGTDPAPGAPC | B-P | 463.1 | -9.3 | 0.472–0.472 |
| 9P95 | 01 | EPGPAGDFWTFG | B-P; A-P | 251.0 | -4.3 | 0.379–0.647 |
| 9P95 | 02 | GSDEIESRFDNA | B-P; A-P | 256.5 | -0.5 | 0.473–0.697 |
| 9P95 | 03 | PWEAHGMTDFMD | B-P; A-P | 203.2 | -4.6 | 0.332–0.759 |
| 9P95 | 04 | YGHYGDDQQPHN | B-P; A-P | 215.3 | -2.0 | 0.325–0.652 |
| 9P95 | 05 | MHHWYNDSSLLK | B-P; A-P | 227.6 | -2.8 | 0.412–0.609 |
| 9P95 | 06 | SAHEWNYSDGEE | B-P; A-P | 164.7 | -1.4 | 0.551–0.551 |
| 9P95 | 07 | LNNHTFCPYAQG | B-P; A-P | 253.8 | -3.3 | 0.561–0.580 |
| 9P95 | 08 | DQNGTSGSGWPM | A-P; B-P | 190.1 | -1.8 | 0.605–0.622 |
| 9P95 | 09 | SPDYGYNGNGYD | B-P; A-P | 217.7 | -1.9 | 0.590–0.634 |
| 9P95 | 10 | GEQGDNGDEGDA | B-P; A-P | 174.8 | -1.3 | 0.414–0.719 |

## Interpretation

PISA reports relatively larger peptide-interface areas and more favorable summed solvation energies for the 6UIB and 8UUI top-10 models than for the 9P95 models. This is a within-run comparison only: each target has a different receptor and PISA is not a calibrated cross-target binding-affinity predictor. The 9P95 results show small peptide–receptor interfaces and mostly weak/near-neutral solvation-energy estimates, consistent with those poses having limited interfacial burial by PISA criteria. This does not establish that the generated structures are experimentally nonbinding.

The service returned `status=Ok` for all 30. In as-is mode it reports the input assembly but does not calculate predicted crystal assemblies; the absence of predicted assemblies is expected and should not be read as a job failure.

## Files

- `jobs.json`: upload status and job IDs.
- `results.json`: raw PISA API outputs for complexes and interfaces.
- `summary.csv`: candidate-level metrics, pair details and result URLs.
- Results remain hosted temporarily according to the PISA privacy policy; use `jobs.json` links while active.
