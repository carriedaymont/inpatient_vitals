# inpatient_vitals

#### This repository contains data tables with heart rate (HR) and respiratory rate (RR) reference values for pediatric inpatients from two publications:

- [HR and RR by age](https://publications.aap.org/pediatrics/article/131/4/e1150/31913/Development-of-Heart-and-Respiratory-Rate): Christopher P. Bonafide, Patrick W. Brady, Ron Keren, Patrick H. Conway, Keith Marsolo, Carrie Daymont; Development of Heart and Respiratory Rate Percentile Curves for Hospitalized Children. Pediatrics April 2013; 131 (4): e1150–e1157. 10.1542/peds.2012-2443
- [HR and RR by age and body temperature](https://publications.aap.org/pediatrics/article/135/5/e1173/33643/Heart-Rates-in-Hospitalized-Children-by-Age-and): Carrie Daymont, Christopher P. Bonafide, Patrick W. Brady; Heart Rates in Hospitalized Children by Age and Body Temperature. Pediatrics May 2015; 135 (5): e1173–e1181. 10.1542/peds.2014-3738. 

### Files available for download, data dictionaries, and notes on their use

##### inpt_hr_zscores and inpt_rr_zscores
These .csv files are lookup tables for reference values from the Bonafide 2013 paper. Each contain 4 columns:
1. months: This is an integer, and refers to the last completed month of age. For example, a child who is 360 days old and is therefore 11.8 months would match to 11 in the months column).
2. hr/rr: Also an integer, corresponds to the vital sign value, ranges from 30-240 for HR and 5-100 for RR.
3. hrz/rrz: The HR-for-age or RR-for-age z-score corresponding to the age and vital sign for that row. Note that for some extreme age/vital sign combinations hrz or rrz is blank.
4. hrp/rrp: The HR-for-age or RR-for-age percentile corresponding to the age and vital sign for that row (converted from the z-score). Note that this percentile ranges from 0 to just under 100, so a value of 0.1 refers to the 0.1st percentile, not the 10th percentile.
<br />


##### inpt_hrz_temp_zscores and inpt_hrz_temp_percentiles
These compressed .csv files are lookup tables for reference values for HR for age and body temperature from the Daymont 2015 paper. It contains 5 columns described below. They are relatively large files when uncompressed (about 50 MB). If you have trouble using these, another option to calculate these z-scores is below.
1. months: An integer, the last completed month of age, as for the inpt_hr_zscores file above.
2. hr: The vital sign value as an integer. For this file the HR ranges from 40-240 for 0 to <2yo and 40-200 for 2 to <18yo.
3. temp: Body temperature in Celsius to the nearest 0.1 degree, ranges from 35 to 40.5.
4. hrz_t or hrp_t: hrp_t is the HR for age and body temperature z-score, and hrz_p is the HR for age and body temperature percentile. As for hrp above, hrp_t ranges from 0 to just under 100, so a value of 0.1 refers to the 0.1st percentile, not the 10th percentile.
<br />


###### inpt_hr_temp_lms
This file and instructions only need to be used used if the inpt_hrz_temp_zscores or inpt_hrz_temp_percentiles above cannot be used because of size. 
This .csv file contains the values needed to calculate HR-for-age-and-temp z-scores. Use of this file requires a few steps (described below). It contains 4 columns:
1. temp: Body temperature in Celsius to the nearest 0.1 degree, ranges from 35 to 40.5.
2. l: L, M, and S are the parameters describing the distribution of HR-for-age at a given body temperature. They are typically capitalized but the column names are lowercase.
3. m
4. s

To get the HR for age and body temperature z-score (hrz_t) for a given age, HR, and body temperature:
1. Get the HR for age z-score (hrz) from the inpt_hr_zscores table above .
2. Get the L, M, and S parameters that match the temperature from the inpt_hr_temp_lms table.
3. Calculate the z-score with the following formula:  hrz_t = ((((hrz + 5) / M) ^ L) - 1) / (L * S). (Those of you who have worked with LMS values before will notice the addition of the "+ 5", you can see note at the end of this readme for more information.)
4. To get the percentile from the z-score, some of the methods you can use are the pnorm() function in R, the normprob function in Stata, or NORM.S.DIST(z, TRUE) in Excel. All of the methods listed will provide a value ranging from 0 to just under 1. To get percentiles matching the range in the hr_centiles and rr_centiles tables, you will need to multiply this value by 100.
<br />


**More information about the reference values is available in the publications (citations and links above). If you have additional questions about the percentiles or how to use these files, please feel free to email me at cdaymont@pennstatehealth.psu.edu.**
<br />
<br />

<sub>Re the +5 in the hrz_t formula: The HR for age and body temperature z-scores are a z-score of HR-for-age z-scores by body temperature (the repeat of the term z-scores is intentional). The software used to make the z-scores (the GAMLSS package in R) could only use positive numbers, but z-scores have a mean of 0. So, we have to add 5 to the HR-for-age z-scores before working with them. The overlap between people using these curves and people who use the growthcleanr algorithm is likely very small, but in case anyone is curious, the "+ 5" in this formula and the "+ 5" in the EWMA formula for the pediatric growthcleanr algorithm each exist for completely unrelated reasons. </sub>
