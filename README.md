# Synthetic Oversampling

This repo cotains the replication package for a recent EMSE journal submission. For full details see [our paper](Synthetic_Oversampling.pdf).

Software analytics becomes challenging when the target class is rare. For example, in some of the data explored here, defects or vulnerabilities occur only once in 32 instances, or even more drastically, once in 88 examples. In such imbalanced data, a learner may become insensitive to the minority targets, as distributions from the majority class dominate data sampling. This problem is well-known as class imbalance problem 
.
One of the promising solution is called the data level solution which performs action on the data instances. Prior literature mainly explores (a) over-sampling, (b) under-sampling, and (c) hybrid-sampling. Over-sampling and under-sampling aim to balance the class ratio by generating synthetic minority samples and removing majority samples correspondingly, while the hybrid-sampling is the mix of the over-sampling and the under-sampling. Our study, due to the highly imbalanced ratio, explores the over-sampling solution.

A recent state-of-the-art study from Shu et al. (MSR 2022) recommend the use of hyper-parameter tuning on generative adversarial networks (GAN). GAN employs two deep learners to synthesize more examples of the minority class. Within GAN, the generator artificially produces fake data, while the discriminator attempts to identify which outputs have been artificially synthesized. Feedback from the discriminator is used to guide and enhance the generator.
In our experiment, we significantly extend Shu et al.’s study by (a) exploring much more over-sampling synthetic data generation algorithms and (b) empirically evaluating these algorithms on more software analytic tasks.


## Replication Steps for RQ1 and RQ2

[Our paper](Synthetic_Oversampling.pdf) offers two research questions: 

- **RQ1** 1: Which synthetic oversampling technique can generate the most informative minority samples, leading to the highest performance for
 learners trained on the original dataset combined with synthetic sam- ples?
- **RQ2** RQ2: When generating the synthetic samples, which approach has the highest efficiency (measured in terms of runtimes)?

To run the code, and the statistical analysis required to answer these questions, pleae apply the following steps. 

1. clone the repo with
   ```
   git clone https://github.com/Anonymity941212/SyntheticOversampling.git
   cd SyntheticOversampling
   ```
2. create virtual environment, active virtual environment, and install required packages (example with Ubuntu 22.04)
   ```
   python -m venv [vir_name]
   source [vir_name]/bin/activate
   pip install -r requirements.txt
   ```

3. Run the experiment with following command
   ```
   python ./src/runner.py [case_study_name] [repeats] [random_projection_threshold]
   ```
   1. **case_study_name**: In the current replication package, we include 6 case studies. The name of these 6 case studies are
         1. JS_Vuln
         2. Ambari_Vuln
         3. Moodle_Vuln
         4. Defect_Eclipse_JDT_Core
         5. Defect_Eclipse_PDE_UI
         6. Defect_Mylyn
         
         Note: If you want to include your own case studies, add your data file into **/data** folder, and create the write folder in both **/result** and **/evaluation** folders. Add your own file reading script in **/src/utils.py** under the **read_data** function started from line 53.
   2. **repeats**: How many repeats you want the experiments to run? We run 10 repeats. You can choose your own from 1 to N (don't make N too large).
   3. **random_projection_threshold**: The parameter to control the size of cluster in recursive random projection algorithm.
  
4. With the results from previous steps, this step cleans the results and save them to multiple txt file for the purpose of doing statistical test
   ```
   python ./src/process_result.py [case_study_name]
   ```
   All processed txt files are saved in the **\evaluation** folder.

5. The last step is to run the statistical test. Please note that the script for statistical test is developed in Python2. Hence you need to have Python2 to run the last step.
   ```
   cat ./evaluation/[case_study_name]/[metric_name]_[learner].txt | python2 ./evaluation/sk_stats.py --text [text_size] --latex [True/False]
   ```
   1. **metric_name**: The metric to evaluate the prediction performance. The available metrics are
        1. auc
        2. recall
        3. fpr
        4. f1
        5. gscore
     
      We use recall, fpr, and gscore in our paper.
   2. **learner**: The name of the machine learning models. They are [Adaboost, DT, GBDT, KNN, LightGBM, LR, RF, SVM]
   3. **text_size**: The text size to display the statistical results in the terminal
   4. **latex**: Whether to display the latex version of generated results.
