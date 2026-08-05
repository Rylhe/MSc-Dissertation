 Evaluating Time-Windowed Learning Behavior Features for Early Student Outcome Prediction Using the Open University Learning Analytics Dataset 

========
 Research Aim and Research Questions
========

 Project title:
 Time-windowed learning behavior representation for early student outcome prediction using OULAD.

 Research aim:
 This study investigates whether representations that preserve the temporal development of students' early learning behavior provide additional predictive value beyond conventional cumulative summaries.

 Main Research Question:
 How effectively can students' final course outcomes be predicted from time-windowed representations of their early learning behavior at different early-course prediction cutoffs in OULAD?
 在 OULAD 中，使用保留时间结构的早期 learning behavior features，
 能在不同 prediction cutoffs 下多有效地预测学生最终课程结果？

 Sub-RQ1: Outcome Representation
 How does the representation of final course outcomes affect early prediction performance?

 The following outcome formulations will be compared:

 A. Four-class multiclass prediction:
    Distinction / Pass / Fail / Withdrawn

 B. Two-stage prediction:
    Stage 1: Withdrawn vs Completed
    Stage 2: Distinction / Pass / Fail among completed students

 Rationale:
 Withdrawn and Fail should not be merged automatically because
 they represent different course trajectories. Withdrawn students frequently show early disengagement and limited assessment participation, whereas students who Fail may remain engaged but achieve insufficient academic performance.

 Sub-RQ2: Feature Representation
 Do time-windowed behavioral features improve early outcome prediction compared with conventional cumulative aggregated features?

 This is the central research question of the dissertation.

 Key comparison:
 Conventional cumulative representation vs Time-windowed behavioral representation

 Sub-RQ3: Prediction Timing
 At which early-course prediction cutoff do students' behavioral records become sufficiently informative for useful final-outcome prediction?

 Prediction cutoffs:
 Week 2
 Week 4
 Week 6
 Week 8

 Corresponding observation periods:
 Week 2: Days 0--13
 Week 4: Days 0--27
 Week 6: Days 0--41
 Week 8: Days 0--55

========
 Prediction Unit and Target
========

 Prediction unit:
 One student registration in one module presentation.

 Unique keys:
 id_student
 code_module
 code_presentation

 Target:
 final_result

 Total prediction units before modelling exclusions:
 32,593 student--module--presentation registrations.

========
 Feature Groups
========

 1. Static Student and Course Features

 Features available at or before the start of the course.

 Examples:
 gender
 age_band
 region
 highest_education
 imd_band
 disability
 studied_credits
 num_of_prev_attempts
 code_module
 code_presentation
 registration timing, where available before the cutoff

 2. Conventional Cumulative Behavioral Features

 Features that summarize all observed VLE behavior from the
 course start to the prediction cutoff.

 Examples:
 cumulative total clicks
 cumulative active days
 cumulative unique VLE sites
 mean clicks per active day
 number or proportion of inactive days

 These features represent the conventional aggregated baseline.

 3. Time-Windowed Behavioral Features

 Features that preserve how engagement develops across individual
 course weeks rather than compressing all activity into one total.

 Examples:
 weekly clicks
 weekly active days
 weekly unique VLE sites
 week-on-week change
 recent-window activity
 engagement trend
 activity variability
 number of inactive weeks

 These features form the main behavioral representation examined
 in the dissertation.

 4. Assessment Behavior Features

 Assessment information observable on or before each prediction
 cutoff.

 Examples:
 number of assessments available before the cutoff
 number of submitted assessments
 assessment participation rate
 early mean score
 first observed score
 number or proportion of late submissions
 mean submission delay
 score-missing indicator

 Assessment features must account for:
 assessment_type
 is_banked
 assessment availability before the cutoff

 Students with no submitted assessments should be retained rather
 than removed.

========
 Feature Representation Experiments
========

 F1: Static features only

 F2: Static + cumulative behavioral features

 F3: Static + assessment features

 F4: Static + cumulative behavioral + assessment features

 F5: Static + time-windowed behavioral + assessment features

 Primary feature comparison:
 F4 vs F5

 This comparison tests whether preserving the temporal development
 of learning behavior adds predictive value beyond conventional
 cumulative activity summaries while holding assessment information
 broadly constant.

========
 Experimental Design
========

 Experiment 1: Outcome Representation

 Compare:
 Four-class multiclass prediction
 versus
 Two-stage prediction

 Purpose:
 To determine whether separating withdrawal from completed-course
 performance provides a more informative and effective prediction
 formulation.

 Experiment 2: Feature Representation

 Compare feature sets F1--F5.

 Main focus:
 Cumulative aggregated features
 versus
 Time-windowed behavioral features

 Purpose:
 To evaluate whether temporal behavior representation contributes
 predictive information beyond simple activity totals and early
 assessment summaries.

 Experiment 3: Early Prediction Timing

 Compare prediction performance at:
 Week 2
 Week 4
 Week 6
 Week 8

 Purpose:
 To identify when early learning records become sufficiently
 informative for practically useful prediction.

========
 Models
========

 Baseline model:
 Logistic Regression

 Role:
 Provides a simple and interpretable linear baseline.

 Tree-based reference models:
 Random Forest
 LightGBM

 Optional:
 XGBoost may be included if computational resources and experiment
 size permit.

 The models are validation tools for evaluating feature
 representations. The dissertation is not designed primarily as
 a classifier-ranking study.

========
 Data Splitting and Preprocessing
========

 The same data-partitioning strategy should be used across feature
 representations and prediction cutoffs to support fair comparison.

 Preferred approach:
 Grouped train/validation/test split using id_student

 Reason:
 The same student may appear in more than one module presentation.
 Grouping by id_student reduces the risk that registrations from
 the same individual appear in both training and test data.

 Additional considerations:
 categorical encoding
 numerical scaling for Logistic Regression
 missing-value indicators
 class imbalance
 consistent random seed
 preprocessing fitted on training data only

========
 Temporal Leakage Prevention
========

 Every prediction dataset must be reconstructed separately at each
 cutoff.

 Only information observable before the cutoff may be used.

 Exclude:
 VLE activity after the cutoff
 submissions after the cutoff
 scores unavailable before the cutoff
 future assessment participation
 full-course click totals
 date_unregistration
 has_unregistered
 any feature derived from the final course outcome

 For VLE records:
 Week 2 includes dates 0--13
 Week 4 includes dates 0--27
 Week 6 includes dates 0--41
 Week 8 includes dates 0--55

 Negative-date VLE activity should not be mixed directly into the
 weekly course windows. It may instead be represented separately
 as pre-course activity if included.

========
 Evaluation
========

 Primary metric:
 Macro F1

 Secondary metrics:
 Weighted F1
 Per-class precision
 Per-class recall
 Confusion matrix
 Balanced accuracy

 Accuracy may be reported as a supplementary metric but should not
 be used as the main measure because the target classes are
 moderately imbalanced.

 ROC-AUC:
 Appropriate mainly for Stage 1 of the two-stage task:
 Withdrawn vs Completed.

 Multiclass ROC-AUC should only be reported if a clearly specified
 one-vs-rest or one-vs-one formulation is used.

========
 Interpretation
========

 SHAP will be used primarily for the tree-based models.

 Interpretation will examine:
 global feature importance
 class-specific feature contributions
 differences across prediction cutoffs
 the relative importance of cumulative and weekly features
 behavioral indicators associated with Withdrawn, Fail, Pass and
 Distinction predictions

 SHAP values will be interpreted as model-based associations rather
 than causal effects.

========
 Methodological Workflow
========

 1. Data Cleaning and Validation
 Clean and integrate the OULAD tables and validate keys, dates,
 missingness and activity totals.

 2. Analytical Sample Construction
 Create one modelling row per student--module--presentation
 registration.

 3. Outcome Representation
 Construct the four-class target and the two-stage target.

 4. Prediction Cutoff Construction
 Build separate Week 2, Week 4, Week 6 and Week 8 datasets.

 5. Feature Engineering
 Generate static, cumulative behavioral, time-windowed behavioral
 and assessment features using only information available before
 each cutoff.

 6. Data Partitioning and Preprocessing
 Apply grouped splitting, categorical encoding, numerical scaling
 and missing-value handling without fitting preprocessing on the
 test data.

 7. Model Training
 Train Logistic Regression and one or two tree-based reference
 models using consistent feature sets and data partitions.

 8. Evaluation
 Compare outcome formulations, feature representations and
 prediction cutoffs using class-sensitive metrics.

 9. Model Interpretation
 Use SHAP and feature importance to examine which early learning
 behaviors contribute to predictions.
