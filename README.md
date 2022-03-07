# AI_hackerthon
This competition was held by Kwangwoon univ. and Dacon.  
The topic is making a ML model which classifies exercise actions from IMU sensors.  
For example, when we get some sensor datas from gyro sensor and accelator sensor our ML model should classifiy that this Exercise motion is push-up from datas.  

## Description
We use Fouier Transform and Kalman Filter for getting more pre-processing datas to increase accurancy of ML model.  
And we uses GRU + LSTM model Because datas were Sequential show good performance used for these two models. 

## Environment 
google colab pro + python 3.8. 

## Prerequisite
Data load from google Drive.  

## Files
kw_hackthon_kalman :  
(version1) :   
(version2) :  


## Usage
 #### Data Augmentation
  * rolling  
  * rotation  
  * permutation  
 #### Variable add
  1.roll_pitch  
  2.energy  
  3.energy_between_acc_gy  
  4.differential  
  5.fourier_transform  
  6.kalman_filter  
 #### scaler
  1.StandarScaler  
 #### model
  1.GRU  
  2.CNN  
 #### cross validation
  1.StratifiedKFold
