# Long-Term Coherence Change Detection (LT-CCD) for SAR-based Damage Assessment

This repository presents a complete workflow for detecting and analyzing spatial patterns of potential structural damage using **Sentinel-1 SAR coherence data**.

## Overview  

 The project is based on a **Long-Term Coherence Change Detection (LT-CCD)** approach, in which post-event coherence is not interpreted as a simple before-versus-after difference, but is evaluated against a statistically defined pre-event reference state. 

In practical terms, **the workflow**:
- estimates how stable each location normally is, 
- how strongly coherence fluctuates there under non-damage conditions,
- and only then assesses whether the observed post-event change is both **strong** and **unusual**. 

This is the main conceptual advantage of LT-CCD and the reason why it is more robust than simple coherence differencing in heterogeneous urban environments. 

The general **methodological logic** follows the project reports: 
- a long-term baseline is constructed from multiple pre-event coherence images, 
- post-event stacks are compared against this baseline, 
- unreliable areas are excluded with a valid monitoring mask, 
- and candidate damage areas can then be refined through temporal confirmation and visual verification. 

### Study Area and Spatial Scope

The current implementation was developed for the analysis of **Tehran**. This is an especially demanding test area for coherence-based damage detection because it combines dense urban structure with mountainous surroundings and locally complex radar acquisition geometry. These conditions make the analysis much more challenging than in flat, homogeneous urban regions. Coherence changes in such an environment cannot be interpreted naively, since they may reflect not only structural disruption, but also terrain effects, local scattering complexity, and other sources of signal instability. For this reason, the workflow is deliberately designed as a **multi-stage analytical framework** rather than as a single-threshold mapping tool.

### Temporal Scope

The project is designed for **multi-temporal analysis**, not for a one-time image comparison. In the Tehran case study, the analysis covers multiple observation dates between **28 February and 8 April**, allowing the workflow to capture the temporal persistence of structural change rather than reacting to a single potentially noisy observation. This temporal depth is essential for LT-CCD. A long-term baseline is constructed from multiple pre-event coherence scenes in order to estimate the normal behavior of stable surfaces, and one or more post-event stacks are then evaluated against this reference. This means that the method is explicitly built to distinguish between short-lived fluctuations and more persistent anomalies that are more likely to correspond to real structural change.
### What the Workflow Produces

It is important to state clearly what the pipeline is intended to deliver. The workflow does **not** produce a direct map of “confirmed destruction.” Instead, it produces a structured set of **SAR-based indicators of structural change** that have been statistically filtered and spatially refined. These outputs can include damage masks, polygons, clusters, and optional building-level summaries, but they should always be interpreted as evidence of anomalous structural change rather than as absolute proof of destruction. This distinction matters because radar coherence is influenced not only by collapse or debris, but also by vegetation dynamics, soil moisture, acquisition geometry, shadowing, layover, and other scene-specific factors. The strength of the repository is therefore not that it eliminates uncertainty, but that it makes the entire detection process explicit, traceable, and open to validation.

## Project Motivation and Scope

The motivation behind this project emerges from a fundamental limitation in satellite-based damage assessment: **optical imagery alone is often insufficient for reliable and timely analysis**. In many real-world scenarios—such as post-conflict environments, natural disasters, or rapidly evolving crisis situations—optical data is constrained by cloud cover, illumination conditions, revisit frequency, and sometimes by the ambiguity of visual interpretation itself. Subtle structural damage is not always clearly visible in RGB imagery, especially in dense urban environments.

Synthetic Aperture Radar (SAR) provides a complementary perspective. Instead of capturing surface appearance, SAR measures the **physical scattering behavior of the scene**, and coherence in particular reflects the **temporal stability of that scattering**. In stable urban areas, coherence remains consistently high over time. When the structure of the surface changes—due to collapse, debris, or reconfiguration—the coherence signal typically drops. The LT-CCD approach builds directly on this principle, transforming coherence loss into a measurable and statistically interpretable indicator of structural change.

What makes this workflow particularly relevant is that it does not rely on a simple before–after comparison. Instead, it constructs a **long-term statistical baseline**, allowing the system to distinguish between expected variability and truly anomalous behavior. This is critical in heterogeneous environments where coherence naturally varies due to terrain, acquisition geometry, or seasonal effects.

From a practical perspective, the repository implements a **research-grade urban damage detection pipeline** based on Sentinel-1 coherence stacks. The workflow integrates several complementary components: baseline statistics, post-event change analysis, masking of unreliable areas, optional temporal confirmation, spatial clustering, and multi-level validation. Additional datasets—such as building footprints and digital elevation models—are used to refine the analysis and reduce false positives caused by terrain effects or geometric distortions.

Although the current implementation is centered on **Tehran**, the scope of the project is broader and can be interpreted along three dimensions:

* **Spatial scope (location)**
  The methodology is demonstrated on a complex urban environment with strong heterogeneity, including dense built-up areas and mountainous surroundings. This makes it a challenging but representative test case.

* **Temporal scope (analysis logic)**
  The workflow explicitly distinguishes between long-term baseline behavior and short-term post-event changes, emphasizing temporal consistency rather than single-date comparison.

* **Methodological scope (transferability)**
  The pipeline is designed as a **modular and reusable framework**. With appropriate preprocessing (i.e., consistent coherence stacks and AOI definition), it can be adapted to other regions, events, or SAR acquisition geometries.


An important aspect of this project is the **multi-stage validation strategy**. While Sentinel-2 imagery is used for systematic visual inspection and cluster-level verification, selected areas were additionally validated using **high-resolution optical imagery** where available. This higher level of detail is particularly useful for:

* confirming ambiguous coherence signals,
* understanding false positives in complex urban structures,
* and supporting the manual calibration of detection thresholds.

This combination of SAR-based indicators with optical validation—both medium and high resolution—ensures that the workflow remains **physically interpretable and empirically grounded**, rather than purely algorithmic.


## What the Method Actually Does

The central methodological idea of LT-CCD can be summarized in one sentence: **damage is inferred as an anomalous loss of coherence relative to normal long-term behavior**. Instead of taking one pre-event coherence image and one post-event coherence image and subtracting them, the method first builds a historical reference model from multiple baseline rasters.

For a given pixel, let the baseline coherence observations be:

$
\gamma^{(1)}_{\text{baseline}}, \gamma^{(2)}_{\text{baseline}}, \dots, \gamma^{(n)}_{\text{baseline}}
$

From this stack, the workflow computes the baseline mean and baseline standard deviation:

$
\mu_{\text{baseline}} = \frac{1}{n} \sum_{i=1}^{n} \gamma^{(i)}_{\text{baseline}}
$

$
\sigma_{\text{baseline}} = \sqrt{\frac{1}{n} \sum_{i=1}^{n} \left(\gamma^{(i)}_{\text{baseline}} - \mu_{\text{baseline}}\right)^2}
$

For the post-event stack, the mean coherence is computed as:

$
\mu_{\text{post}} = \frac{1}{m} \sum_{j=1}^{m} \gamma^{(j)}_{\text{post}}
$

The first change indicator is the coherence difference:

$
\Delta \gamma = \mu_{\text{post}} - \mu_{\text{baseline}}
$

This tells us whether coherence dropped after the event, and by how much. However, this value alone is not sufficient. A drop of \(-0.15\) may be highly significant in a stable neighborhood and completely unremarkable in a noisy or unstable one. For this reason, the workflow also computes a normalized anomaly score:

$
Z = \frac{\Delta \gamma}{\sigma_{\text{baseline}}}
$

The interpretation is simple and powerful. A pixel is interesting when the coherence drop is both **large in magnitude** and **unexpected given the local historical variability**. This dual logic is what differentiates LT-CCD from simple change detection. In the repository, a pixel is flagged as a candidate damage pixel only if it satisfies threshold conditions on both gamma and Z, while also passing a valid monitoring mask.

This mask is not a technical detail; it is one of the most important parts of the pipeline. Only pixels with sufficiently high baseline coherence, sufficiently low baseline variability, valid post-event behavior, and optionally acceptable terrain slope are retained. In other words, the workflow deliberately excludes areas where coherence is unstable even under normal conditions. This design choice reflects the methodological description of the project: the approach is not intended to interpret every change as damage, but to search for strong structural anomalies in places that are normally stable enough to monitor meaningfully.


## End-to-End Workflow

The repository is designed as a **coherent analytical pipeline**, where each stage addresses a specific methodological requirement and produces structured outputs that are consumed by the next stage. Rather than treating the notebooks as independent scripts, the workflow should be understood as a **sequential system for transforming raw SAR coherence data into validated spatial damage indicators**.

At a high level, the pipeline progresses from **data standardization → statistical change detection → spatial refinement → validation → parameter calibration → temporal interpretation**. Each of these stages is implemented as a dedicated notebook, with clearly defined inputs, outputs, and configuration parameters.

### **1. Data Preparation and Grid Standardization**

*Notebook: `LT-CCD Data Preparation`*

The first stage focuses on transforming raw Sentinel-1 coherence rasters into a **consistent analytical dataset**. This step is critical, because any stack-based statistical analysis requires strict spatial alignment across all inputs.

The notebook scans the input directories (both baseline and post-event), identifies valid coherence rasters (`*_corr.tif`), and constructs a unified processing grid. A reference raster is selected via:

* `reference_source` (either `"post"` or `"baseline"`)
* `reference_index` (position in the stack)

All rasters are then:

* reprojected to a common CRS,
* resampled (typically using `Resampling.bilinear`),
* aligned to a shared transform and resolution,
* and clipped to the AOI.

The output consists of **standardized raster stacks** stored in structured directories (`prepared/post` and `prepared/baseline`), along with metadata files such as:

* `prepared_raster_inventory.csv`
* `preparation_summary.json`

This stage ensures that all downstream computations operate on a **pixel-wise comparable dataset**, which is a strict requirement for LT-CCD.

### **2. LT-CCD Computation and Damage Detection**

*Notebook: `LT-CCD Damage Estimation`*

The second stage implements the **core LT-CCD methodology**. It operates on the prepared raster stacks and computes the statistical indicators used for damage detection.

After validating stack consistency (CRS, shape, transform), the notebook constructs:

* baseline mean coherence (`mean_baseline`)
* baseline standard deviation (`std_baseline`)
* post-event mean coherence (`mean_post`)

From these, two key indicators are derived:

* **Delta coherence**
  Δγ = γ_post − γ_baseline

* **Z-score**
  Z = Δγ / σ_baseline

These indicators are evaluated only within a **valid monitoring mask**, defined through parameters such as:

* `baseline_mean_min` (e.g., 0.78)
* `baseline_std_max` (e.g., 0.10)
* `post_mean_min` (artifact suppression)
* optional `slope_threshold_deg` (terrain filtering)

Damage is detected using threshold-based criteria:

* `delta_threshold` (e.g., −0.2)
* `z_threshold` (e.g., −2)

An optional but important refinement is **temporal confirmation**, controlled by:

* `use_temporal_confirmation = True`

In this case, a second post-event stack is processed independently, and only detections that persist across both dates are retained. This step significantly reduces noise and transient false positives.

The outputs include:

* raster products (delta, z-score, masks)
* vectorized damage polygons
* optional building-level damage statistics

### **3. Spatial Post-Processing and Clustering**

*Notebook: `LT-CCD Results Filtering`*

Even after statistical filtering, coherence-based detection can produce **spatially fragmented results**. The third stage introduces spatial structure through clustering.

Polygons are first cleaned and filtered by area using:

* `min_polygon_area_m2` (e.g., ~1600 m²)

Clustering is then performed using **DBSCAN**, applied to polygon centroids in a metric CRS:

* `dbscan_eps_m` (maximum distance between neighbors)
* `dbscan_min_samples` (minimum cluster size)

This allows the workflow to:

* group nearby detections into coherent clusters,
* identify and optionally remove isolated polygons (`keep_only_clustered = True`),
* and generate dissolved cluster geometries for easier interpretation.

The result is a more **spatially meaningful representation of damage patterns**, reducing noise and emphasizing consistent structures.

### **4. Visual Verification and Manual Labeling**

*Notebook: `LT-CCD Cluster Visual Interpretation`*

At this stage, the workflow transitions from purely algorithmic detection to **human-in-the-loop validation**.

Detected clusters are visualized using:

* Sentinel-2 pre-event imagery
* Sentinel-2 post-event imagery

For each cluster, the notebook:

* extracts buffered image chips (`buffer_m`, e.g., 100–200 m),
* displays side-by-side comparisons,
* overlays cluster boundaries,
* and allows manual classification.

Clusters are labeled using fields such as:

* `review_status` (yes / no / uncertain / skip)
* `review_type` (destruction / false_positive / urban_change / etc.)

This stage plays a crucial role in:

* identifying systematic errors,
* understanding detection behavior,
* and providing the **first approximation for threshold calibration**.

### **5. Quantitative Validation and Threshold Calibration**

The fifth stage builds on the manually labeled dataset and performs a **quantitative analysis of detection performance**.

For each reviewed cluster, the workflow extracts:

* mean delta coherence
* mean z-score
* baseline and post-event coherence levels

The distributions of **true detections vs. false positives** are then compared using:

* histograms
* boxplots
* ROC-like threshold analysis

This enables:

* evaluation of class separability,
* identification of optimal threshold ranges,
* and refinement of parameters such as `delta_threshold` and `z_threshold`.

In practice, this creates an **iterative calibration loop**:

1. Start with initial thresholds
2. Visually inspect results (Notebook 4)
3. Analyze distributions (Notebook 5)
4. Adjust parameters
5. Repeat

This iterative process is essential because **optimal parameters are not universal**. They depend on:

* SAR acquisition geometry (orbit, incidence angle)
* local urban structure
* terrain complexity
* temporal baseline characteristics

### **6. Temporal Aggregation and Time-Series Visualization**

The final stage extends the analysis across multiple dates and provides a **temporal perspective on detected damage patterns**.

Results from different processing runs (e.g., multiple post-event dates) are combined and visualized using an interactive **Folium time-slider map**.

Key features include:

* conversion of clusters into circle-like geometries (scaled by area),
* temporal encoding of detections,
* interactive exploration of spatial evolution.

This allows users to:

* track persistence of damage signals over time,
* identify stable vs. transient detections,
* and better interpret the dynamics of structural change.


## Data Description

The analysis is built around several complementary geospatial datasets, each serving a different role in the workflow.

* The primary inputs are **Sentinel-1 coherence rasters**, derived from SLC-based processing and stored as single-band GeoTIFFs with a `_corr.tif` suffix. These are separated into baseline folders and post-event folders. The baseline stack defines normal coherence behavior. The post-event stacks represent the periods that are tested for potential structural change. The quality, temporal spacing, and consistency of these stacks strongly influence the reliability of the results.

* The **AOI layer** is used to define the spatial extent of analysis and to clip all prepared rasters. This ensures that every step works on exactly the same study region and avoids unnecessary processing outside the target area.

* A **DEM** is used to derive slope. This is especially important for Tehran, where the topographic context is not negligible. In radar data, steep or geometrically complex terrain can introduce local distortions, shadowing, layover, and unstable coherence patterns. Slope masking is therefore not just an optional visual enhancement; it is a key quality-control mechanism in this project.

* Optional **building footprints**, derived for example from OpenStreetMap, allow aggregation from pixel-level or polygon-level detections to more meaningful urban objects. This is especially useful when the final application is not only mapping but also estimating potential building impacts.

* The validation stage uses **Sentinel-2 optical imagery** to visually inspect candidate clusters. The optical data do not replace SAR; rather, they provide an independent visual context that helps distinguish plausible destruction from false positives related to geometry, local urban change, or other processes.

## Validation Strategy and Parameter Tuning

One of the most important practical lessons of this project is that **LT-CCD parameters should not be treated as universal constants**. Thresholds such as minimum baseline coherence, maximum baseline variability, delta coherence threshold, z-score threshold, polygon area threshold, DBSCAN distance, or slope threshold depend strongly on the study area, the orbit, the quality of the coherence stack, and the local scattering environment.

For that reason, the repository should not be used as a black box. The recommended calibration logic is iterative and deliberately includes manual review.

A sensible first step is to start with a physically reasonable parameter set and run the first three notebooks to obtain candidate damage clusters. Then use **Notebook 4** as a first approximation. Visual inspection of pre-event and post-event Sentinel-2 image chips often reveals very quickly whether the thresholds are too permissive or too conservative. If most clusters look implausible, the detection needs to be tightened. If obvious damaged areas are missing, the thresholds may be too strict.

After this first visual pass, **Notebook 5** should be used to analyze the reviewed clusters quantitatively. Because it compares the statistical distributions of reviewed positives and reviewed negatives, it becomes much easier to see which variables provide the clearest separation. In some AOIs, z-score may be the dominant discriminator; in others, the baseline coherence threshold or the post-event mean may matter more. This notebook is therefore the natural place to refine the parameter set after the initial visual interpretation.

In practical terms, the repository supports the following tuning strategy:

1. prepare and run the pipeline with an initial threshold set,  
2. inspect the detected clusters visually in Notebook 4,  
3. label clusters as likely true damage, false positive, unclear, or related change,  
4. analyze class separation in Notebook 5,  
5. adjust thresholds and rerun the pipeline.

This process is especially important when changing **orbit**, **AOI**, or **temporal setup**. A parameter set that works reasonably for one Sentinel-1 track or one urban region may not transfer directly to another. This is not a weakness of the method; it is a realistic consequence of SAR physics and scene-specific coherence behavior.

## Discussion of Current Performance

For the Tehran case study, the current practical validation suggests an approximate accuracy on the order of **75–80%**. This should be interpreted carefully. The figure reflects the present validation setup and the complexity of the problem rather than a universal benchmark. Tehran is not an easy test area. The number of truly destroyed locations is not extremely large, which makes the signal-to-noise balance more difficult, and the surrounding terrain introduces additional coherence instability through geometry and slope effects.

These conditions explain why the pipeline includes several layers of refinement beyond simple thresholding. In flatter or more homogeneous urban regions, performance may differ. Likewise, in an AOI with a larger number of well-defined destruction zones, threshold calibration may be easier. The current results for Tehran therefore suggest that LT-CCD can be operationally useful, but only when accompanied by contextual interpretation and validation rather than by purely automatic reporting.

Another important point is that the validation framework itself is designed to be transparent. Instead of producing a single opaque performance number, the repository supports manual review, threshold comparison, exploratory histograms, class-wise boxplots, and temporal inspection. This makes it easier to understand not just how well the method works, but **why** it succeeds or fails in specific situations.

## Repository Structure

The project is organized around raw data, prepared inputs, notebooks, and result products. A simplified structure looks as follows:

```text
LT_CCD_damage/
├── Data/
│   ├── Row_Data/
│   │   ├── SLC_1/
│   │   ├── SLC_2/
│   │   ├── Teheran_AOI/
│   │   ├── Teheran_DEM/
│   │   ├── Teheran_OSM/
│   │   ├── Teheran_S2/
│   │   └── Teheran_Validation/
│   └── Prepared_data/
│       ├── SLC_1/
│       └── SLC_2/
│
├── Pipeline/
│   ├── 01_LT_CCD_data_preparation.ipynb
│   ├── 02_LT_CCD_damage_estimation.ipynb
│   ├── 03_LT_CCD_results_filtration.ipynb
│   ├── 04_LT_CCD_visual_interpretation.ipynb
│   └── 05_LT_CCD_false_positiv_analysis.ipynb
│   └── 06_LT_CCD_visualisation.ipynb
│
├── Results/
│   ├── SLC_1/
│   ├── SLC_2/
│   ├── circle_geometries/
│   └── teheran_damage_timeslider_circles.html
│
├── LT_CDD_reports/
├── README.md
└── requirements.txt
```

The exact names of some directories may differ slightly depending on the local project version, but the analytical logic remains the same: raw inputs are stored separately from prepared stacks, the notebooks form the processing backbone, and outputs are written into structured result folders.

## Installation

The project is implemented in **Python** and relies on a set of standard geospatial and scientific libraries commonly used in remote sensing workflows. The pipeline does not require GPU acceleration and can be executed on a standard workstation, although sufficient RAM is recommended for handling raster stacks.

A typical environment should include:

* Python 3.10 or later
* `numpy`
* `pandas`
* `geopandas`
* `rasterio`
* `shapely`
* `matplotlib`
* `scikit-learn`
* `folium`
* `jupyter`

A minimal setup using **conda** can be created as follows:

```bash
conda create -n ltccd python=3.10
conda activate ltccd

pip install numpy pandas geopandas rasterio shapely matplotlib scikit-learn folium jupyter
```

## Outputs

One of the strengths of the repository is that it preserves the **full analytical chain**, rather than producing only a final result. Outputs are generated at multiple levels, enabling both interpretation and reproducibility.

At the **raster level**, the pipeline produces:

* mean baseline coherence
* baseline standard deviation
* mean post-event coherence
* delta coherence
* z-score
* slope (if terrain filtering is enabled)
* valid monitoring mask
* binary damage masks

At the **vector level**, outputs include:

* damage polygons
* clustered polygons
* dissolved cluster geometries
* optional building-level damage layers

At the **tabular level**, the workflow exports:

* processing summaries (`.json`)
* raster inventories (`.csv`)
* reviewed cluster datasets
* parameter configurations

These files document both the inputs and the applied settings, which is essential for reproducibility and further analysis.

At the **visualization level**, the repository provides:

* raster preview plots
* Sentinel-2 comparison panels (pre/post event)
* cluster review layers
* interactive HTML time-slider maps

## Limitations

The workflow is intentionally transparent about its limitations. LT-CCD does not directly detect “damage” in a semantic sense; it detects **statistically significant changes in coherence**, which may be associated with structural change.

A drop in coherence can be caused by:

* structural destruction
* vegetation changes
* soil moisture variation
* radar acquisition geometry
* complex urban scattering effects

These factors are particularly relevant in **mountainous terrain and dense urban environments**, where interpretation becomes more challenging.

For this reason, the pipeline incorporates several safeguards:

* terrain filtering (slope mask)
* temporal confirmation
* spatial clustering
* manual visual validation

These components are not optional add-ons but essential parts of making the method usable in practice.

Users should therefore treat the pipeline as a **robust analytical framework**, not as a one-click classification tool. The quality of the results depends strongly on:

* baseline quality
* stack consistency
* AOI-specific parameter tuning
* validation strategy

## License

This project is licensed under the MIT License.

## Citation

```bibtex
@misc{ltccd_tehran_2026,
  author       = {Anton Chaika},
  title        = {LT-CCD Damage Detection Pipeline: Long-Term Coherence Change Detection for SAR-based Damage Assessment},
  year         = {2026},
  publisher    = {GitHub},
  note         = {Sentinel-1 coherence-based urban damage detection workflow},
  howpublished = {\url{https://github.com/vertical-52/ltccd_damage_assessment}}
}
```

## Contact

For questions, feedback, or collaboration opportunities, please reach out via:

- GitHub: [antonchajjka](https://github.com/antonchajjka)
- Email: antonchajjka@gmail.com