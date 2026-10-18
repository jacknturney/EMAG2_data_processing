# EMAG2 magnetic anomaly enhancement --- New York region

Exploratory processing of the Earth Magnetic Anomaly Grid (EMAG2) to
investigate whether shorter-wavelength magnetic anomalies and potential
geological structures can be distinguished from dominant regional
anomalies along the New York and adjacent US Atlantic margin.

The workflow applies **reduction to the pole (RTP)**,
**regional--residual separation**, **total horizontal derivative
(THDR)** and **tilt derivative (TDR)** filtering. Removing
longer-wavelength magnetic variations may help reveal localised magnetic
sources of geological or mineral-exploration interest that are otherwise
obscured by regional anomalies.

> **Interpretation disclaimer:** Candidate lineaments are preliminary
> magnetic interpretations, **not confirmed faults, lithological
> contacts or mineral deposits**. Geological maps and independent
> constraints from higher-resolution magnetic, gravity, seismic and/or
> borehole data are needed to test their geological significance.

## Outputs

![Four-panel magnetic anomaly
comparison](EMAG2_RTP_Residual_THDR_TDR_4Panel.png)

*Original RTP, residual RTP (10 km Gaussian smoothing), THDR and TDR
maps.*

![Candidate magnetic
lineaments](EMAG2_Annotated_Candidate_Lineaments.png)

*Illustrative candidate lineaments L1--L4. These labels are visually
interpreted rather than quantitatively extracted or validated.*

The accompanying report is
**`EMAG2_RTP_Workflow_Two_Page_Report_Annotated_Revised.pdf`**.

## Processing workflow

1.  **Import and project data.** Read EMAG2 magnetic anomaly data in XYZ
    format and work in WGS84 / UTM Zone 18N (EPSG:32618).
2.  **Grid the anomalies.** The input points have approximately 2.8--3.0
    km spacing. Use linear interpolation onto a 1.5 km grid, retaining a
    mask outside the data footprint. This resampling does **not**
    improve the intrinsic source-data resolution.
3.  **Reduce to the pole.** Apply a regularised Fourier-domain RTP
    transformation to reduce the asymmetry of magnetic anomalies under
    the assumption of predominantly induced magnetisation. The
    experiment used a 2025 reference field with inclination **+65.0°**
    and true declination **−12.3°**, with correction for UTM grid
    convergence. Reflective padding and tapering help limit boundary
    effects.
4.  **Separate regional and residual fields.** Smooth the RTP grid using
    a **10 km Gaussian standard deviation** and subtract the smoothed
    component. This is a gradual wavelength filter, **not** a sharp 10
    km wavelength cutoff.
5.  **Calculate THDR.** Compute the magnitude of the horizontal
    gradients of the residual field to highlight lateral changes in
    magnetisation.
6.  **Calculate TDR.** Combine the first vertical derivative with THDR
    using an arctangent normalisation; examine TDR zero contours
    alongside THDR gradient highs.
7.  **Visualise and interpret.** Compare original RTP, residual RTP,
    THDR and TDR on a common map extent with US coastlines and state
    boundaries. Inspect coincident trends as **candidate magnetic
    lineaments**.

## Requirements

Python 3 with:

``` bash
pip install numpy pandas scipy matplotlib pyproj cartopy
```

The processing uses NumPy/SciPy FFT and interpolation routines, while
Cartopy provides coastline and state overlays. Cartopy may download
Natural Earth features on first use.

## Running the workflow

Run the project's Python scripts or notebook **in processing order**,
ensuring the input XYZ path points to your local copy of the data. The
workflow described in the report uses these intermediate arrays:

  Array          Description
  -------------- -------------------------------------------------
  `X`, `Y`       UTM Easting and Northing meshgrids, in metres
  `Z_linear`     Linearly interpolated magnetic anomaly grid, nT
  `Z_rtp`        Reduced-to-pole anomaly grid, nT
  `Z_regional`   Gaussian-smoothed RTP grid, nT
  `Z_residual`   RTP minus regional field, nT
  `THDR`         Total horizontal derivative, nT/km
  `TDR`          Tilt derivative, degrees

If the code is split into separate scripts, save/load intermediate grids
(for example, as `.npz` files) between stages. Update filenames and
paths to match the files actually committed to the repository.

## Interpretation and limitations

The residual field highlights shorter and locally segmented anomalies
that are less apparent in the regional RTP field. Some gradients
coincide between THDR and TDR, including features near the New
York--Long Island area and along the offshore magnetic corridor. These
may indicate magnetised-body boundaries, basement heterogeneity, igneous
sources or structural contacts, but are **not uniquely diagnostic** of
any particular geology.

Important limitations include the EMAG2 compilation's heterogeneous
input coverage, the original \~3 km sampling interval, assumptions
underlying RTP, amplification of noise by derivatives, and possible
artefacts near grid boundaries and missing-data areas. Features should
be checked for persistence across smoothing scales and compared against
**published geological maps** before being interpreted as faults,
intrusive bodies or mineralisation.

## EMAG2 data source and citation

**Confirm which EMAG2 release was used before finalising the citation.**
The local XYZ filename alone does not establish whether it originated
from the original EMAG2 release or EMAG2v3. Both are listed here to
avoid misattribution.

**If using EMAG2v3 (version 3):**

> Meyer, B., Saltus, R., & Chulliat, A. (2017). *EMAG2v3: Earth Magnetic
> Anomaly Grid (2-arc-minute resolution)*. Version 3. NOAA National
> Centers for Environmental Information.
> https://doi.org/10.7289/V5H70CVX

Associated methods paper:

> Meyer, B., Chulliat, A., & Saltus, R. (2017). Derivation and error
> analysis of the Earth Magnetic Anomaly Grid at 2 arc min resolution
> version 3 (EMAG2v3). *Geochemistry, Geophysics, Geosystems, 18*,
> 4522--4537. https://doi.org/10.1002/2017GC007280

**If using the original EMAG2 release (version 2):**

> Maus, S. (2009). *EMAG2: Earth Magnetic Anomaly Grid (2-arc-minute
> resolution)*. Version 2. NOAA National Centers for Environmental
> Information. https://doi.org/10.7289/V5MW2F2P

Associated methods paper:

> Maus, S., et al. (2009). EMAG2: A 2--arc min resolution Earth Magnetic
> Anomaly Grid compiled from satellite, airborne, and marine magnetic
> measurements. *Geochemistry, Geophysics, Geosystems, 10*, Q08005.
> https://doi.org/10.1029/2009GC002471

Dataset metadata: [NOAA
EMAG2v3](https://www.ncei.noaa.gov/access/metadata/landing-page/bin/iso?id=gov.noaa.ngdc.mgg.geophysical_models%3AEMAG2_V3)
· [NOAA
EMAG2](https://www.ncei.noaa.gov/access/metadata/landing-page/bin/iso?id=gov.noaa.ngdc.mgg.geophysical_models%3AEMAG2)

## Related geological context

-   Turney, J. N., et al. (2026). Identifying basaltic flows and sills
    in buried Mesozoic rift basins along the eastern US seaboard using
    seismic interpretation and geopotential modelling. *Geophysical
    Journal International*. https://doi.org/10.1093/gji/ggag085
-   Turney, J. N., Goldberg, D. S., & Tinto, K. J. (2026). New
    constraints on the structure of offshore Mesozoic rift basins and
    distribution of the Central Atlantic Magmatic Province along the
    eastern U.S. coastline from high-resolution aeromagnetic and gravity
    data. *Frontiers in Earth Science*.
    https://doi.org/10.3389/feart.2026.1800394

## Reproducibility and reuse

The repository documents an **exploratory geophysical workflow**, not a
validated mineral-resource assessment or definitive structural map.
Record the exact EMAG2 version, source grid, processing settings,
software versions and geographic extent when reproducing the results.

Add a repository licence if you intend others to reuse your original
code and figures. The EMAG2 dataset remains attributed to its original
publisher and authors.
