---
title: 'Code detailing of CIMS data reduction procedures'
date: 2025-01-09
permalink: /CANLAB-archive2k23/code-data-red-1/
---
## Tips for S1 & S2 codes
The S1 (step 1) & S2 (step 2) codes are used for obtaining the raw data in the below steps inter the start & stop folder to compiles all the folders in those dates. For example the below code looks for files in the folders with dates 1106,1107,1108. If you want to compile single date use the same date for both start and stop.
 
 <pre>
% Will only look at the folders within this time frame
start_folder = 20241106; 
stop_folder = 20241108;
</pre>

Next step is keeping eye on the for loop index. Change this number based on number of dates. For example in the above date this index goes from 1 to 3. If doing for a single date this is just one.
<pre>
    for fd_idx = 1:3
</pre>

<span style="color: red;">Caution: If there are folders with names 20241106, 20241106_20241107 then you need to increase the number based on number of folders not dates.</span>

## Detailing of S3 code

The step 3 which is for separating sampling states between zero air & ambient sampling. This can be done based on two identification factors, first one being the zero flow change (0&rarr;2000 sccm). Second one being the opening and closing of the zero valve.Based on the type of operation that you have performed during the sampling. The best would be usage of the valve which reduces the efforts to find the errors. The major problem with the zero flow is due to a time lag shiof that occurs between the set value and the monitor value. Second one is the zero flow takes a couple seconds to reach 2000 sccm from zero with some in between flow values. Here we generally use the reagent ion as the tracer ion to keep track of all the changes.

<pre><code class="language-matlab">
%% Define parameters to separate stats
IMR_p_min = 54.5; % min allowable IMR pressure
IMR_p_max = 55.5; % mas allowable IMR pressure
</code></pre>
Look for the fluctuations in the IMR pressures during the sampling of ambient air. Input the values for the minimum and maximum pressures that the IMR pressure reaches during the sampling.

<pre><code class="language-matlab">
pnt_rm_amb_begin = 100; % how many data points to remove at the beginning of the ambient period
pnt_rm_bkg_begin = 155; % how many data points to remove at the beginning of the bkg period
</code></pre>

This number can be bit tricky use the defulat values in the code and run the code. Next see the plots and see whether you need to increase the number of data points, to check for the zero period stabilization.

### The sub function used in S3 separate states function

<pre><code class="language-matlab">
function [state_key,f1] = separate_states_CROCUS_v240730(HK_data_all,MS_all,Formula_all,tracer_ion_str,fd,MS_type,varargin)
</code></pre>
This function is what gives out the separate state keys.In this we use the house keeping data from the raw files which was generated using the S2. The good and bad data are basically separated using IMR pressure ranges, if it goes below/above  0.5 mbar of set pressure then that is considered as bad data. The zero period and the ambient period are separated either using zero valve data (open/close) or zero flow monitor data.

<pre>
% Combine the data data
flag_bad = flag_bad_1 | flag_bad_2 | flag_bad_3;
% Create the finalized flags for good ambient, bkg, and cal
flag_amb = flag_amb & ~flag_bad;
flag_bkg = flag_bkg & ~flag_bad;
flag_cal = flag_cal & ~flag_bad;
</pre>

The total bad periods are divided into three parts bad_1 (from IMR pressure), bad_2 (removal of bad data at the start of ambient sampling ) & bad_3 (removal of bad data at the start of the background sampling)

Similary if there is daily calibration in between the sampling , then calibration points also can be separated using the cal valve or the cal flow monitor.


























