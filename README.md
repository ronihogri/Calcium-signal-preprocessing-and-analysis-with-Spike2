# Data processing and extraction from multi-cellular calcium imaging with Spike2
A program using the [Spike2 scripting language](https://ced.co.uk/products/spkovsl) to process calcium imaging data. The program cleans the data, visualizes it, and extracts features of the detected calcium signals. It creates two output files: a txt file holding the clean data values for all cells, and a csv file containing a summary of the results obtained.  
Roni Hogri, August 2023
<br><br>
This program was used in the recently published study: 
Mussetto, V., Teuchmann, H. L., Heinke, B., Trofimova, L., Sandkühler, J., Drdla-Schutting, R., & Hogri, R. (2023). Opioids Induce Bidirectional Synaptic Plasticity in a Brainstem Pain Center in the Rat. *The Journal of Pain*.
For additional context and related results, please [see publication](https://www.sciencedirect.com/science/article/pii/S1526590023004054) (Fig. 3, in particular). 
<br><br>
## Background
In many brain cells (e.g., neurons, astrocytes), the level of activity is correlated with the amount of intracellular calcium. These calcium signals can be quantified in imaging experiments. Typically, many cells are imaged simultaneously, and their calcium activity is analyzed offline. One major challenge in these experiments is that the experimental medium (e.g., a brain slice in a bath) is not completely stationary. Thus, movements in all 3 directions are possible, leading to was is known as ‘signal drift’. Therefore, analyzing calcium signals requires pre-processing steps that correct for this drift. 
The Spike2 program ‘Automated_calcium_analysis.s2s’ processes data from text files that were produced by the image processing program ImageJ. In these text files, each column represents a channel (brain cell), and each row represents the intensity of the calcium signal recorded from this cell per 1 second period. The example text file (‘example_recording_15chans_cocktail.txt’) contains data from 15 cells recorded for 6 minutes. The first 30 seconds of the recording are a baseline period, after which a substance is applied to the brain tissue. The application of the substance is expected to result in elevated intracellular calcium in some of the recorded cells beginning ~30 seconds later. 
<br><br>
## How it works
The program cleans the calcium data, visualizes it, and extracts features of the detected calcium signals. It creates two output files: a txt file holding the clean data values for all cells, and a csv file containing a summary of the results obtained. 
To facilitate code readability for people who don't have Spike2 installed, I also enclose a PDF version of the program file: ‘Automated_calcium_analysis.s2s.pdf’. For detailed information on the Spike2 scripting language, please see this [manual](https://ced.co.uk/img/Spike9.pdf). 
The main steps of the program are described and shown below. For additional details, please comments within the program file. 
 <br><br>
## Steps:
*Before starting, the user should review and adjust the ‘user-defined variables’ as needed (see program file), then run the program on Spike2. 

1. The program prompts the user to select the txt data file to be processed, and reads this file.

2. For each column (brain cell) in the txt file, the program uses a running window to correct for drift over time. For each time point (row), raw intensity values are converted to dF/F, where F is the time window under consideration and dF is the difference between F and the current time point.

3. For visualization and inspection purposes, dF/F values are imported into a Spike2 .smr data file, named after the original txt file.

4. If specified by the user, a high-pass filter is applied to further correct for drift over the recording time.

![Image](https://github.com/ronihogri/Calcium-signal-preprocessing-and-analysis-with-Spike2/blob/main/smr%20waveforms.png) 
   Example showing dF/F values from the 15 cells included in the 'example_recording_15chans_cocktail.txt' file. In this case, a high-pass filter (0.001 Hz, 2nd order Bessel filter) was applied to further correct for drift over time. The vertical line represents the time point at which a drug was applied (30 seconds into the recording).

5. The mean and standard deviation of dF/F values during the baseline period (first 30 seconds) are used to calculate the threshold amplitude. Any signal that exceeds this threshold for a defined minimal duration (e.g., 5 seconds) is considered a 'calcium event'. Detected events are visualized in the .smr data file. An example of 5 channels is shown below, with calcium events detected in channels 1-4. The duration of events is marked in the corresponding 'Events' channels, above the waveforms.

![Image](https://github.com/ronihogri/Calcium-signal-preprocessing-and-analysis-with-Spike2/blob/main/waveforms%20and%20events.png) 

6. For each calcium event, the program extracts the duration, peak amplitude, and area over threshold.

7. Two new files are created (see example files enclosed):

   i. A text file ending with '_drift_corrected.txt', holding all data points for all cells as dF/F values.

      ii. A CSV file ending with '_SUMMARY.csv', which contains the summary of the main results from the cells included in the analysis. For each cell in which calcium events were detected, the duration, peak amplitude, and area over threshold of calcium events are reported.
