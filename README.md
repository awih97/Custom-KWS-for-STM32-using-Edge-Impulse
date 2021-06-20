# Custom-KWS-for-STM32-using-Edge-Impulse

This guide will help you deploy a custom KWS system on any STM32 MCU (in this tutorial we will be using STM32F44NRE). [Edge Impulse](https://www.edgeimpulse.com/) enables developers to create their own intelligent device solutions with embedded Machine Learning. You will learn how to easily acquire wav dataset samples using your own voice, train your ML algorithm and deploy to your devices. 

# Requirements

- [Stm32 NUCLEO boards](https://www.st.com/en/evaluation-tools/stm32-nucleo-boards.html)
- Sign up for a free [Edge Impulse account](https://www.edgeimpulse.com/) 
- [Stm32IDE development tool](https://www.st.com/en/development-tools/stm32cubeide.html)
- [INMP441 MEMS Digital Microphone](https://www.digikey.my/en/product-highlight/i/invensense/inmp441-mems-digital-microphone)

# Prerequisites

Make sure you followed the [Home Automation keywords tutorial](Home-Automation-traininig/README.md), and have a trained impulse.

# Overview

Creating your Edge Impulse project
In this tutorial we'll build a model that can distinguish between different type of commands: `On Lamp 1`, `on lamp 2`, `off lamp 1` and `off lamp 2`.

![image](https://user-images.githubusercontent.com/57432755/122665445-d49b7b80-d1d9-11eb-9a9e-9b17fa1be7f3.png)


# Set up STM32IDE 

Open your STM32IDE > Create `new project` and find `SMT32F44NRE boards` > give `Project name` > choose `C++` > click `Finish`

![image](https://user-images.githubusercontent.com/57432755/122664732-c4819d00-d1d5-11eb-9b98-06c25454bfe1.png)

![image](https://user-images.githubusercontent.com/57432755/122664740-cb101480-d1d5-11eb-8a0f-830db2d5bb23.png)

# Enabling CRC

The CRC needs to be enabled.
  1. Go to **Pinout & Configuration**.
  2. Select **Computing > CRC**.
  3. Enable the **Activate** checkbox.

![image](https://user-images.githubusercontent.com/57432755/122664860-6ef9c000-d1d6-11eb-9035-b876c7e9f66b.png)


# Adding the CMSIS-PACK

From the [Home Automation keywords tutorial](Home-Automation-traininig/README.md), you will get `.pack` file which is full library contains of the impulse and all external required libraries to  be deployed in your boards.

  1. Go to **Help > Manage embedded software packages**.
  2. Select **From Local** and select the `.pack` file you just downloaded.
  3. Accept all the license agreement and it will be installed. 

![image](https://user-images.githubusercontent.com/57432755/122664992-3b6b6580-d1d7-11eb-8011-0d5d7878b494.png)

  4. Back to the **Pinout Configuration**.
  5. Click **Software packs > Select Components.
  6. Select your project, expand the pack andd checkbox under selection core.

![image](https://user-images.githubusercontent.com/57432755/122665052-a2891a00-d1d7-11eb-95af-b2ccbb4997a1.png)

# Set up I2S configuration

We need to set up I2S for INMP441 MEMS Digital Microphone to receive audio wav and feed input to our boards.

  1. Back to the **Pinout Configuration**.
  2. Select **Multimedia > I2S1**.
  3. Select mode **Half-Duplex Master**.
  4. Set Transmission Mode to **Mode Master Receive"

![image](https://user-images.githubusercontent.com/57432755/122665176-52f71e00-d1d8-11eb-9f99-b025779ed6f0.png)

# Configure clocks and Generate code

  1. Open **Clock Configuration**.
  2. Set **180Mhz for clocks**.

![image](https://user-images.githubusercontent.com/57432755/122665488-1c220780-d1da-11eb-9fb8-102fcffb1ddb.png)

  4. To generate code **Half-Duplex Master**, from keyboard press `alt+k` and click yes. Afterward you should have a 'Middleware' folder in your project with your impulse and all required libraries.
  5. The code generator always generates a new main.c file, even for C++ projects. So, we need to rename our `main.c` to `main.cpp`.

![image](https://user-images.githubusercontent.com/57432755/122665613-ce59cf00-d1da-11eb-8401-4be01927f906.png)


# Test Run the impulse library

Now, we can try to test run our library use from impulse to make sure results is comparable to impulse results.
To debug you can use many ways for examples using [printf through SWV trace](https://www.youtube.com/watch?v=sPzQ5CniWtw) or any other external software.

To make easier, we will just use live expression from STM32IDE.

Open `main.cpp` and add following code under `USER CODE BEGIN Includes`. 
     #include "edge-impulse-sdk/classifier/ei_run_classifier.h"
     using namespace ei;

     // paste the raw features here
     int features[] = {
         13, -5, 1, -13, -15, -13, -5, 0, -8,  ...
     };

     int get_feature_data(size_t offset, size_t length, float *out_ptr) {
         memcpy(out_ptr, features + offset, length * sizeof(float));
         return 0;
     }
     
Here we will initialize raw data from audio wav of our commands (in practice use later, we will change to mems microphone input). To get the raw data, head back to your edge-impulse site and go to `Live Classification`. Then load any validation sample such as `onlamp2` label and copy over the Raw features and paste to the code below.

![image](https://user-images.githubusercontent.com/57432755/122666295-006d3000-d1df-11eb-90ae-58241951db6d.png)
     
Then, initialize all variables: each variables have it function as commented beside it,

    /* USER CODE BEGIN 0 */
     float classified = 0;    //Check % classification of each commands, >0.7 consider the results
     int label_command=0;     //number of labeling for each commands     
     int no_result=0;         //which command detected
     
     //indicates which commands detected by got value 1
     bool offlamp2 =0 ; 
     bool offlamp1=0;
     bool onlamp2=0;
     bool onlamp1=0;
     bool noise =0;
     bool unknown =0;
     
     bool trigger_lamp1=0;    //give real output from the board to lamp 1
     bool trigger_lamp2=0;    //give real output from the board to lamp 1
     
     // paste the raw features here
     float features[16000] = {
                                13, -5, 1, -13, -15, -13, -5, 0, -8,  ...
      };
     
     int get_feature_data(size_t offset, size_t length, float *out_ptr) {
           memcpy(out_ptr, features + offset, length * sizeof(float));
           return 0;
      }
      
     /* USER CODE END 0 */
     


And under `USER CODE BEGIN WHILE`, add:

     /* USER CODE BEGIN WHILE */
      while (1)
        {
          /* USER CODE END WHILE */


        signal_t signal;
        signal.total_length = sizeof(features) / sizeof(features[0]);
        signal.get_data = &get_feature_data;
      
     
         ei_impulse_result_t result = { 0 };
         EI_IMPULSE_ERROR res = run_classifier(&signal, &result, true);
          

          for (size_t ix = 0; ix < EI_CLASSIFIER_LABEL_COUNT; ix++) 
          {
               label_command++;

                classified = result.classification[ix].value;
                
                if(classified > 0.7)
                {
                    no_result=label_command;
                    
                    switch(no_result) 
                    {
                      case 1:
                        noise=1;
                        break;
                      case 2:
                        offlamp1=1; trigger_lamp1=0;
                        break;
                      case 3:
                        offlamp2=1; trigger_lamp2=0;
                        break;
                      case 4:
                        onlamp1=1; trigger_lamp1=1;
                        break;
                      case 5:
                        onlamp2=1; trigger_lamp2=1;
                        break;
                      case 6:
                        unknown=1;
                        break;
                      default:
                      offlamp2=0; offlamp1=0; onlamp2=0; onlamp1=0; noise=0; unknown=0; trigger_lamp1=0; trigger_lamp2=0;
                    }
                  
                  }

                }

        
          //trigeer real output to lamp 1 and 2
          if(trigger_lamp1)HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_RESET); else HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_SET);
          if(trigger_lamp2)HAL_GPIO_WritePin(GPIOA, GPIO_PIN_8, GPIO_PIN_RESET); else HAL_GPIO_WritePin(GPIOA, GPIO_PIN_8, GPIO_PIN_SET);

          label_command=0; //reset label
          HAL_Delay(1000);
        /* USER CODE END 3 */
       
      }

Let me explain about this while loop. First when entering while loop, raw features initialize will go through signal processing MFCC and then run the classifier to identify each commands. All of it already includes from .pack edge-impulse.

Then, in the **for loop**, `EI_CLASSIFIER_LABEL_COUNT` is number of keywords existing in our library. For this, we have 6 label which is `On Lamp 1`, `on lamp 2`, `off lamp 1`, `off lamp 2`, `noise` and `unknown`.

It will loop to detect which label has higher percentage of classification which is from this `classified = result.classification[ix].value;`. Any label which get higher than 0.7, will be regarded as the results.

So in the **if(classified > 0.7)**, `no_result` detect which label it is and trigger each label in the **switch case**. (Later we will talk about how to know which no_result for which labels)

Lastly, at the end of while loop is `HAL_GPIO_WritePin` to give real output from boards if it detect `onlamp1 or onlamp2` to 0 (Active low) and `offlamp1 or offlamp2` to 1.

Select **Run > Debug Configuration** and set to ST-link. Then, build project and flash the application to your development board. **Project > Build Project** and **Run > Debug**.

![image](https://user-images.githubusercontent.com/57432755/122668075-049e4b00-d1e9-11eb-8f6a-dbce0d5c2cf1.png)

After Debug, select **Window > Show View > Live Expression** and insert all variables to view.

![image](https://user-images.githubusercontent.com/57432755/122668196-94dc9000-d1e9-11eb-8889-7d23e3ef120d.png)

Now, we can resume our debug and view the results in live expression.

First result, we debug with raw data for unknown commands. We can see in the box the classification of unknown is **0.996** and **no_result = 6**. These mean it detect unkwown commands in label 6 which in the switch case earlier unknown is set to `case 6: unknown=1; break;`.

![image](https://user-images.githubusercontent.com/57432755/122668292-edac2880-d1e9-11eb-9c2e-c0a0ccf8c9d1.png)

Next example result, we change the raw input from **unknown** to **onlamp2** and the result give **no_result = 5"" which mean `onlamp2` is label at label 5

![image](https://user-images.githubusercontent.com/57432755/122668264-cb1a0f80-d1e9-11eb-9b6b-250151bf5763.png)

 If you make your own dataset and different commands from this tutorials, that mean your label will be different from this tutorial and cannot use the **switch case** function here. What you can do is source the raw input for each commands you ahve you have and observe each commands give which **no_result** value. Then you can change the **switch case** for your own projects.
 
 


