# Final Project Proposal

**Lauren Choi & Chloe Robinson**

## Problem Definition & Use Case

Extreme heat is one of the most dangerous climate-related risks in cities. It affects vulnerable groups more than others, such as low-income communities, the elderly, and people who do not have access to cooling systems. As cities are more crowded and climate change makes heat waves worse, urban planners and public health organizations need accurate, detailed information about where heat exposure is most.

Using satellite images and machine learning, this project aims to map how the city is exposed to heat. The target user is a municipal planning or public health organization that wants to figure out which locations should be given priority for interventions like adding more trees, building cooling facilities, or making transit infrastructure more shaded.

The key output of this project will be a classification map identifying areas of high vs. non-high heat exposure, derived from satellite observations of land surface conditions. This output is intended to support decision-making related to:

- Targeting heat mitigation investments
- Prioritizing tree planting or green infrastructure
- Identifying neighborhoods requiring emergency heat response

The model does not directly measure how vulnerable people are. Instead, it finds patterns of heat exposure in the environment. These patterns can then be used with demographic data to provide a better picture of vulnerability.

## Technical Justification

### a. What the Model is Learning

The model is not directly detecting “heat vulnerability,” but rather learning relationships between surface characteristics observable via satellite imagery and heat intensity. A positive prediction (high heat exposure) corresponds to areas that exhibit combinations of features associated with higher land surface temperatures, such as low vegetation cover, high impervious surface concentration, sparse shading, and dense built environments.

These features are derived from spectral reflectance and thermal emission captured by satellite sensors. For example, vegetation strongly reflects near-infrared (NIR) radiation, while built-up surfaces tend to retain and emit heat, especially in thermal infrared bands. Thus, the model will effectively learn a mapping from spectral and derived features to heat conditions, rather than directly identifying socially vulnerable populations.

### b. Task Type

This project will use a supervised classification approach, specifically binary classification of high heat vs. non-high heat areas. This task type is appropriate for the project because the user needs a map with priority areas that support decision-making rather than exact temperature values. Also, classification simplifies interpretation for both specialized and non-specialized audiences and policy applications.

An alternative could be a regression model to predict continuous land surface temperature. Regression can provide more detailed output, yet it is less immediately actionable for planning decisions and creates additional complexity in evaluation and interpretation. Another alternative can be single threshold-based classification such as temperature thresholds, but this would lack flexibility and would not generalise well across different urban environments.

### c. Potential Failure Modes

Land surface temperature does not necessarily mean experienced air temperature or heat exposure. For example, an outdoor parking lot can be extremely hot but have low human exposure if it is rarely used. Also, it will not affect people that much as people usually do not stay in parking lots for long periods of time.

Also, there can be temporal and generalizability limitations. Satellite images provide conditions at a specific time of day so this may not well-represent peak heat conditions or daily heat exposure patterns. A model trained on one specific city may not perform well in different cities with different climate conditions due to changes in vegetation, atmospheric conditions, or land cover. This underscores the significance of careful evaluation and cautious interpretation and implementation of results.

## Methodological Precedent

This project builds on existing methods in remote sensing and urban climate analysis.

### a. Landsat-based Urban Heat Studies

Many research uses Landsat thermal infrared data to find the temperature of the land surface to identify urban heat islands. These methods usually use radiative transfer equations and focus on how heat patterns, vegetation, and impervious surfaces are related. But one of the limitations can be its moderate spatial resolution of 30m. It would not have an image that is as clear as higher resolution data such as STAC.

### b. NDVI-based Urban Heat Analysis

There are many studies that show inverse relationships between NDVI and surface temperature. Vegetation indices such as NDVI are commonly used as proxies for cooling centers, yet NDVI alone fails to spot built environment complexity and the thresholds can vary by region. Thus, NDVI can be used as a feature, but not just as a sole predictor.

### c. Random Forest for Land Cover and Environmental Mapping

Random Forest is frequently used in remote sensing for classification applications due to its efficiency and robustness. It is very effective when integrating various spectral bands and derived indices. The evaluation methodologies in these investigations frequently encompass confusion matrices, F1 scores, and cross-validation. Though it is commonly used, it can have challenges with extrapolation beyond training data and requires careful sampling to avoid spatial bias. This helps build the modeling approach and evaluation design.

## Data Plan

Primary data sources for this project are mainly Landsat 8 and optionally Sentinel-2 imagery. Landsat 8 imagery with spatial resolution of 30m will include thermal infrared bands for heat estimation. Sentinel-2 imagery with higher spatial resolution (10-20m) can also be useful for vegetation and built-up indices. Data for summer months for recent years with preference of cloud-free scenes or median composites will be used in terms of temporality.

## Modelling Approach

### a. Preprocessing

The modeling process will begin by preprocessing summer Landsat 8 imagery. To prepare the satellite data, any clouds and shadows need to be removed, the image clipped to the area’s boundary, and all spectral and thermal bands need to be aligned to a common spatial grid and to the same resolution. Then define how “high heat” vs. “non-high heat” will be assigned by using land surface temperature and labeling pixels above a percentile threshold; top 20% as high heat.

### b. Feature Engineering

The model learns from multiple predictor variables from the satellite imagery including raw spectral bands from Landsat 8, near-infrared (NIR), shortwave infrared (SWIR), and thermal bands. NIR reflects healthy plants, SWIR is sensitive to moisture and helps distinguish between wet and dry surfaces, and thermal bands measure the energy that is emitted from the surface, with hotter surfaces emitting more energy.

In addition to the raw inputs, the model also uses derived indices such as NDVI to measure vegetation density, NDBI to indicate built-up areas, and NDWI to capture moisture or water presence. Other features such as neighborhood averages, local texture, or distance to green spaces, may be included to help the model understand the surrounding environment rather than just relying on the individual pixels.

### c. Baseline Model & Preferred Model

A simple baseline will be established using NDVI thresholding or a logistic regression model, allowing comparison against a more complex approach. A Random Forest classifier is the preferred model for this project. This model is a good fit because it can handle correlated inputs like spectral bands and indices, capture nonlinear interactions between environmental factors and heat exposure, and work effectively with a moderate amount of training data.

Random Forest also gives information about the relevance of different features, which can help figure out which environmental characteristics, such as vegetation or built-up intensity, have a major impact on predicting heat patterns. Its strength and ease of use also make it a good choice for a policy-related use.

### d. Alternative Models & Why We Didn’t Choose Them

We looked at a few different models, but we did not choose any of them. We did not use a basic threshold-based method that only used NDVI or temperature as the main method because it makes rigid assumptions and would not work well in different types of urban areas in terms of generalizability. We also thought about using a linear or logistic regression model, but it might not be able to show the complicated, nonlinear relationships between land cover and heat patterns.

We did not choose more advanced models like gradient boosting or deep learning architectures since they need more tuning, computing power, and training data, and they might make it harder to understand the results.

### e. Model Outputs

The model's outputs will include a raster map that shows where each pixel is located and whether or not it is exposed to excessive heat. The model will also provide each forecast a probability score that shows how reliable it is, which can be used to find regions of uncertainty. You can see these outputs as heat exposure maps and layer them on top of other geographical data, such as neighborhoods or infrastructure, to help make decisions. The final output will be easy to understand and use.

## Evaluation Strategy

The model’s performance will be evaluated by comparing the model’s predictions to the true labels of high vs. non-high heat using accuracy, precision, recall, F1-score, and a confusion matrix. These metrics are calculated based on counts of correct and incorrect predictions, allowing us to assess both overall performance and how well the model identifies high heat exposed areas.

An 80/20 train/validation split will be used, where 80% of the labeled pixels are used to train the model and 20% are kept for model evaluation. This allows the model to learn the relationships between the spectral features and the high vs. non-high heat exposed labels using the training data. The model will then be tested on the unseen validation pixels, which shows how accurately it can generalise the learned patterns to new unseen areas.

In order to determine where the model makes incorrect predictions, an error analysis will be conducted. These errors can include both false positives, which are areas predicted as high heat exposure that are not actually exposed to high heat, and false negatives, which are areas of high heat but were missed by the model. To do this, we will compare the predicted labels to the true labels by identifying which pixels were misclassified, and analyse them by spatially mapping them across our study area.

The performance of the Random Forest model will be compared to the original baseline model to evaluate whether the added complexity improves classification accuracy and reliability. The model will be considered useful if it can consistently identify high heat exposed areas that can support decision-making, even if it still makes some small errors.
