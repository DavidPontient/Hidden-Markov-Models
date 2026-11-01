# Hidden-Markov-Models
Modeling Human Activity States with a Hidden Markov Model
Modeling Human Activity States Using Hidden Markov Models

Formative 2, individual work by David Pontient, African Leadership University.

I recorded my own accelerometer and gyroscope data with Sensor Logger on an iPhone 13, turned it into time and frequency features, and built a Hidden Markov Model from scratch with numpy. The model uses Baum-Welch for training and Viterbi for decoding, and it finds four hidden activity states: standing, walking, jumping and still.

My use case is a platform that makes home construction in Rwanda more transparent. A phone on a worker's belt could show active work and idle time on a building site.

What is in this repository
hmm_activity.ipynb is the notebook with all the code and the saved outputs
data/train/ has the cleaned, labeled CSV files for training, in one folder per activity (12 standing, 12 jumping, 13 walking, 13 still)
data/test/ has the two unseen test recordings, test-01.csv and test-02.csv
raw_zips/ has the original Sensor Logger zip files that the notebook reads
figures/ has the plots made by the notebook
results/ has the metrics table, confusion matrix, transition matrix and the feature table
Formative2_HMM_Report.pdf is the short report
Data format

Every CSV has these columns: timestamp, seconds, acc_x, acc_y, acc_z, gyro_x, gyro_y, gyro_z and activity. All files are at 100 Hz. The original phone rate was about 99.6 Hz, and the notebook brings every file to 100 Hz with linear interpolation. Training files had 0.5 seconds cut from the start and the end. In the test files, rows between two activities are labeled transition.

How I processed the data
I merged the accelerometer and the gyroscope on one regular 100 Hz time grid.
I cut each recording into windows of 2 seconds (200 samples) with 50 percent overlap.
For each window I computed 16 features, 8 per sensor: mean, standard deviation, RMS, signal magnitude area, correlation between axes, dominant frequency, spectral energy and spectral entropy. The last three come from the FFT.
I took the log of the size features and then applied z-score normalization, using the training windows only.
The model
Hidden states: standing, walking, jumping, still
Observations: the 16 normalized features of each window
Emissions: one Gaussian per state with a diagonal covariance
Training: Baum-Welch in log space, stopped when the gain in log-likelihood per window is below 0.0001 (it stopped at iteration 11)
Decoding: Viterbi

Because each training file holds only one activity, I built 60 longer training sequences by joining 4 to 6 recordings in a random order. The transition matrix therefore reflects how I joined the files, not a real day of behavior.

Results on the two unseen test files

I scored 63 windows. 61 were correct, so the overall accuracy is 0.9683.

Activity	Samples	Sensitivity	Specificity	Accuracy (one vs rest)
Standing	16	1.0000	0.9574	0.9683
Walking	15	1.0000	1.0000	1.0000
Jumping	19	1.0000	1.0000	1.0000
Still	13	0.8462	1.0000	0.9683

Both mistakes are the last still window of a test file, decoded as standing. At the end of each recording I reached for the phone to stop it, so that window contains a little real movement. The test files come from the same phone, the same person and the same day, so this does not prove that the model works for other people.

Videos of the data collection

Each video is 5 seconds and shows my full body and my face while I record the activity.

Standing: [paste link here]
Walking: [paste link here]
Jumping: [paste link here]
Still: [paste link here]
How to run the notebook
Install Python and the packages: pip install numpy pandas matplotlib jupyter
Keep hmm_activity.ipynb and the raw_zips folder together in one folder.
Open the notebook and choose Run All. It creates the data, figures and results folders again.

The notebook reads the original zip files in raw_zips. The CSV files in data are the cleaned copies that it writes.
