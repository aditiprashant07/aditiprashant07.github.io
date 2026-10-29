---
layout: page
title: "Near-Real-Time Deforestation Detection"
permalink: /projects/deforestation-detection/
---

# Near-Real-Time Deforestation Monitoring with Sentinel-2 and U-Net

## 1. Project overview

This project implements an end-to-end geospatial computer-vision pipeline to identify possible forest-canopy loss by comparing satellite imagery from two time periods. It focuses on a study area in Rondônia, Brazil, using European Space Agency (ESA) Sentinel-2 multispectral surface-reflectance imagery from 2022 and 2024.

The workflow combines Google Earth Engine for imagery acquisition and cloud filtering, Python geospatial processing for raster verification and tiling, a four-channel U-Net segmentation model for forest-cover classification, post-classification comparison for change detection, and a Streamlit/Folium dashboard for displaying candidate deforestation areas.

Rather than relying on ordinary RGB imagery alone, the model uses Blue, Green, Red, and Near-Infrared (NIR) bands. The pipeline converts detected changes into geospatial polygons and estimates cleared area in hectares.

**Repository:** [Deforestation-Detection](https://github.com/aditiprashant07/Deforestation-Detection)  
**Notebook:** [deforestation_pipeline.ipynb](https://github.com/aditiprashant07/Deforestation-Detection/tree/main/notebooks)  
**Dashboard:** [app.py](https://github.com/aditiprashant07/Deforestation-Detection/blob/main/app.py)

## 2. Objectives

- Build a reproducible pipeline for comparing forest cover across two dates using satellite imagery.
- Acquire Sentinel-2 Level-2A surface-reflectance data for 2022 and 2024 using Google Earth Engine.
- Reduce cloud contamination with QA60 cloud/cirrus masking and multi-date median composites.
- Use multispectral information, including NIR, to classify forest and non-forest pixels at a nominal 10-metre resolution.
- Generate forest-cover masks for both dates and identify candidate canopy loss through post-classification comparison.
- Convert detected change regions into GeoJSON polygons and estimate their area in hectares.
- Present imagery, alert boundaries, and summary metrics through an interactive map dashboard.

## 3. Were the objectives achieved?

**The repository documents an end-to-end implementation design and includes dashboard assets and a GeoJSON output; however, model accuracy and the real-world correctness of individual alerts are not established by the README alone.**

The documented pipeline covers data acquisition, cloud masking, temporal compositing, raster alignment checks, NDVI-derived pseudo-labels, four-channel U-Net segmentation, sliding-window inference, binary change comparison, morphological filtering, polygonization, area calculation, and dashboard presentation. The repository structure also lists `app.py`, `display_2022.png`, `display_2024.png`, and `deforestation_alerts.geojson`.

The README does not provide a held-out ground-truth evaluation, precision/recall, IoU/Dice scores, or a verified comparison against independently labelled deforestation events. Consequently, it is fair to say the project implements a documented detection and visualization workflow, but not to claim a particular model accuracy or that every mapped alert represents confirmed deforestation. The NDVI threshold used to generate pseudo-labels is a proxy for vegetation, not independent ground truth.

## 4. Concepts learned and applied

- **Remote sensing and multispectral imagery:** interpreting Sentinel-2 bands and understanding why NIR helps distinguish vegetation from bare ground.
- **Google Earth Engine:** filtering satellite image collections by location, date, and scene cloud percentage, and performing cloud masking and compositing on the server.
- **Cloud and cirrus masking:** using QA60 bit flags and bitwise operations to exclude contaminated pixels.
- **Temporal compositing:** using dry-season median composites to reduce the effect of clouds, shadows, and transient scene conditions.
- **GeoTIFF and geospatial metadata:** working with coordinate reference systems, raster dimensions, transforms, pixel alignment, and spatial resolution.
- **Vegetation indices:** computing NDVI and change in NDVI as signals for investigating vegetation loss.
- **Image tiling:** splitting large multi-band rasters into 256 × 256 patches to make training and inference more manageable on limited GPU memory.
- **Semantic segmentation:** using U-Net to classify each pixel rather than assigning one label to an entire image.
- **Transfer learning:** using an ImageNet-pretrained ResNet-34 encoder as the U-Net backbone.
- **Loss functions:** combining BCEWithLogitsLoss with Dice loss to account for pixel-level classification and mask overlap.
- **Post-classification comparison:** comparing forest masks from two dates to identify pixels classified as forest initially and non-forest later.
- **Morphological processing:** using binary opening to reduce isolated noise in the change mask.
- **Raster vectorization and area calculations:** converting raster detections to GeoJSON polygons and estimating cleared area from pixel area.
- **Geospatial visualization and application development:** presenting map layers and summary metrics through Streamlit and Folium.
- **Limits of proxy labels:** understanding that NDVI threshold labels can introduce bias and should not be treated as manually verified ground truth.

## 5. Approach and what is different about it

### Approach

1. **Acquire satellite imagery.** Query the Sentinel-2 harmonized surface-reflectance collection in Google Earth Engine for a defined area of interest in Rondônia, Brazil, for July–August 2022 and July–August 2024.
2. **Filter and composite observations.** Apply scene-level cloud-percentage filtering, mask opaque clouds and cirrus with QA60, select B2/B3/B4/B8, and calculate median composites for each time period.
3. **Verify raster compatibility.** Load exported GeoTIFFs with Rasterio and check coordinate reference systems and dimensions before performing pixel-wise comparisons.
4. **Prepare training patches and labels.** Tile the imagery into 256 × 256 patches and create initial forest/non-forest pseudo-labels from an NDVI threshold. This provides a scalable starting point but introduces label noise.
5. **Train a multispectral segmentation model.** Configure a U-Net with a ResNet-34 ImageNet-pretrained encoder and four input channels, then optimize a combined binary cross-entropy and Dice loss.
6. **Detect changes.** Run inference for both dates and apply post-classification comparison: pixels classified as forest in 2022 and non-forest in 2024 become candidate loss areas.
7. **Clean and vectorize the results.** Apply a 3 × 3 morphological opening to reduce isolated noise, convert connected change regions into GeoJSON polygons, and estimate polygon areas in hectares.
8. **Display the output.** Use Streamlit and Folium to show the two time-period layers, candidate deforestation boundaries, parcel-level areas, and summary metrics.

### What makes this approach distinctive?

- **Multispectral rather than RGB-only analysis:** adding NIR provides a vegetation-sensitive signal that ordinary visible-light imagery does not capture.
- **A temporal comparison rather than a single-image classifier:** comparing predicted masks from two dates focuses the workflow on change, not simply whether a pixel looks forested in one image.
- **An end-to-end geospatial workflow:** satellite data acquisition, model inference, change extraction, vectorization, area estimation, and map-based presentation are connected in one project.
- **Compute-aware design:** Google Earth Engine handles large-scale imagery filtering/compositing, while tiling makes model training and inference more practical on a Colab GPU.
- **Operationally accessible results:** GeoJSON outputs and a Streamlit/Folium interface make candidate change regions easier to inspect than raw raster predictions alone.

These are architectural strengths, not proof of superior detection accuracy. NDVI pseudo-labels can confuse some vegetation types, and differences in image registration, seasonality, cloud masking, or land cover can create false alerts. Independent ground-truth validation is required to measure reliability.

## 6. Technologies and tools used

| Technology | Role in the project |
|---|---|
| Python | Main language for the processing pipeline and dashboard |
| Google Earth Engine | Satellite collection filtering, cloud masking, temporal compositing, and export |
| ESA Sentinel-2 L2A / S2_SR_HARMONIZED | Multispectral satellite surface-reflectance imagery |
| Google Colab | Notebook-based development and GPU-backed experimentation |
| Google Drive | Storage and transfer of exported satellite rasters |
| NumPy | Array operations, tiling, and mask processing |
| Rasterio | GeoTIFF reading, geospatial metadata, raster windows, and polygonization |
| PyTorch | Deep-learning model training and inference |
| segmentation-models-pytorch | U-Net architecture and pretrained ResNet-34 encoder |
| SciPy | Morphological filtering of binary change masks |
| GeoPandas | Geospatial vector-data handling |
| GeoJSON | Storage and interchange format for detected polygons |
| Streamlit | Interactive dashboard application |
| Folium | Interactive map and layer visualization |
| Matplotlib | Visual inspection and plotting of imagery and results |

## Limitations and next steps

- Evaluate on independently labelled forest-change samples and report precision, recall, F1, and IoU/Dice for the segmentation and change-detection stages.
- Compare alerts with authoritative deforestation reference data or manually reviewed samples.
- Use spatially and temporally appropriate train/validation/test splits to reduce leakage and overly optimistic evaluation.
- Investigate whether the QA60 masking strategy adequately handles cloud contamination in the selected dates and imagery collection.
- Document the effect of the NDVI pseudo-label threshold and assess alternatives, since NDVI alone is not a definitive forest label.
- Check pixel-level co-registration, projection, and area calculations in an appropriate projected CRS before interpreting hectares as precise measurements.
- Describe dashboard results as candidate deforestation alerts until independently validated. The pipeline is a monitoring prototype, not a substitute for field verification or an authoritative enforcement system.
