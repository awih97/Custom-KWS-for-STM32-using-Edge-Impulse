# Creating-your-Edge-Impulse-project

In this tutorial we'll build a model that can do simple tasks in home automation like: `On Lamp 1`, `on lamp 2`, `off lamp 1` and `off lamp 2`.

# 1. Connect devices
>Note: if you already have dataset of wav files, you can use the [Edge Impulse Uploader](https://docs.edgeimpulse.com/docs/cli-uploader) to add images to your project.

Head to your Edge Impulse project and under devices tab you can connect your devices:

you can either connect to your PC microphone or mobile pohone to capture saound for your training data.

![image](https://user-images.githubusercontent.com/57432755/122660725-fc78e800-d1b5-11eb-85fe-a1b4d9481558.png)

# 2. Collecting your first-data

With your device connected we can collect some data. In the studio go to the Data acquisition tab. This is the place where all your raw data is stored, and collecting data from your devices.

># By using Mobile Phone
Under Record new data, select your device, set the `label to onsample1`, the `sample length to 30000` (30 seconds), the `sensor to microphone` and the `frequency to 16000Hz`. You can later edit these labels if needed.
![image](https://user-images.githubusercontent.com/57432755/122660825-16ff9100-d1b7-11eb-9346-4fbae83d7684.png)

># By using your computer
you will be guided to new tab in browser. Set the `label to onsample1`, the `sample length to 30000` (30 seconds) on the top.
![image](https://user-images.githubusercontent.com/57432755/122661043-e4569800-d1b8-11eb-896b-dbc2545f470c.png)


Start sampling your data by continuosly say the word `on lamp 1` with small gap between each say.

Afterward, you will have wav data like this.

![image](https://user-images.githubusercontent.com/57432755/122661132-c8072b00-d1b9-11eb-8726-7c83dc60cfa5.png)

You will need to cut out the parts where you say your keyword. This is important because you only want the actual keyword to be labeled as such, and not accidentally label noise, or incomplete sentences. Easier ways to do is Click `⋮` next to your sample, and select `Split sample` and it automatically split each keywords or you can manually adjust it. For this examples, it will split and give 18 wav data for `onlamp1` label.

![image](https://user-images.githubusercontent.com/57432755/122661194-2f24df80-d1ba-11eb-8b98-7e7a16ce6ec8.png)


Repeat these steps as many dataset you want!

# 3. Building your dataset


After you got all dataset keywords collected, in addition to your trainig you will also need other data than your keywords. as such background noise, fan noise, people muttering other word which we can conclude with noise and unknwown keywords. You can collect these data like in step 2 but better ways is by using pre-built dataset provided by edge-impulse. You can download it [here](https://docs.edgeimpulse.com/docs/keyword-spotting).

To import this data, go to Data acquisition, click the Upload icon, select file you downloaded and select all files respectively for 'noise' and 'unknown' samples (there's 25 minutes of each class, but you can select less files if you want), set the label and clicking Begin upload. 

![image](https://user-images.githubusercontent.com/57432755/122661374-2e8d4880-d1bc-11eb-81cd-184f5125cc5b.png)

Last step in building your dataset, you will need to rebalance your dataset for training and testing class. This is important to make sure your training and testing enough to get higher accuracy. To do this, go to your `dashboard` and select `Rebalance dataset`. this will automatically split your data between training(80%) and testing(20%) class.

![image](https://user-images.githubusercontent.com/57432755/122661586-7ca34b80-d1be-11eb-841f-bd9ac9f10244.png) ![image](https://user-images.githubusercontent.com/57432755/122661591-87f67700-d1be-11eb-8ba3-b6656d8bffea.png)


# 4. Designing your impulse

With dataset we got now, we can run our Neural Network for classifying it. Impulse edge take the raw data from our dataset, use signal processing to extracts features and then learning block to classify data. 

For this tutorial, we will convert the wav data to [MFCC](https://en.wikipedia.org/wiki/Mel-frequency_cepstrum#:~:text=From%20Wikipedia%2C%20the%20free%20encyclopedia,nonlinear%20mel%20scale%20of%20frequency.). MFCC stands for Mel Frequency Cepstral Coefficients which convert raw wav data (mostly redundant information) into simplified forms. you also can choose other methods,  including "MFE" and the "Spectrogram" blocks for non-voice audio, but the "MFCC" block is great for dealing with human speech.

Then, the features from MFCC will be feed to Neural Network blocks for modeling by using [keras network](https://www.tutorialspoint.com/keras/keras_introduction.htm). 

In impulse, add Time series, an audio MFCC, Neural Network(Keras) and save impulse.

![image](https://user-images.githubusercontent.com/57432755/122661725-e40dcb00-d1bf-11eb-96c9-0e2a0be75196.png)

# 5. Configure MFCC block

We can configure each blocks to our desire. Click on the MFCC tab. Set parameters for MFCC or you just can use the default value. In this tab, we can see on top is our raw wav data, parameters for MFCC and the left side shows visualization of our raw data converted to MFCC or known as [spectogram](https://pnsn.org/spectrograms/what-is-a-spectrogram#:~:text=A%20spectrogram%20is%20a%20visual,energy%20levels%20vary%20over%20time.). MFCC spectogram highlighted high frequencies in human speeches.

In vertical axis represents frequencies which is controlled by coefficient in MFCC parameters while horizontal axis are time controlled by frame stride and fram length. Same keywords will have almost same MFCC spectogram depending on various peoples but will be different for other keywords. that's why we need Neural Network which can learn to classify each keyword from various peoples.

![image](https://user-images.githubusercontent.com/57432755/122661925-cb9eb000-d1c1-11eb-8d7a-833a167e2332.png)

# 6. Configure Neural Network

With all the dataset, processed with MFCC, it's time to start training your NN. NN algorithms, modeled to imitate human brain which can recognize patterns. The input of this NN is using MFCC features shich is generated by the previous step. Click on `NN Classifier ` in the left tab and you can set parameters for NN or just use the default one. Here we select for data augmentation which can run more training cycles and increase accuracy.

![image](https://user-images.githubusercontent.com/57432755/122663348-4e2c6d00-d1cc-11eb-9361-9cb587d471c1.png)

>Note: You can learn more on [how Neural Network works](https://www.explainthatstuff.com/introduction-to-neural-networks.html) and others [type of Neural Networks](https://www.mygreatlearning.com/blog/types-of-neural-networks/)



After all done, click `Start training` and wait for the performance report.

![image](https://user-images.githubusercontent.com/57432755/122663357-5dabb600-d1cc-11eb-9d18-c847afbb3b5d.png)

Now, you've done trained a NN with edge-impulse!. 

On the left side, you can see accuracy which NN try to classify each datasets while the table shows percentage how many it can classify correct and wrong. For most applications, up to 85% accuracy is good enough. You can source more dataset or rerun your training and change the parameters of NN to increase your acuuracy if possible. The on-device information is to show usage of this model in your devices and how long it takes to run in your boards.


# 7. Classifying new data

Before we can deploy this model into our boards, it importants to test our model on new datasets (different input from training). Luckily, we already separate 20% of our datasets into teting class. With this, open Model testing and run `classify selected`.

![image](https://user-images.githubusercontent.com/57432755/122663555-e70fb800-d1cd-11eb-9d5f-486a6700eab8.png)

Here, it will give same output as in step 6. If there are mismatch in your classificatio, you can click the `⋮` and check the classification to refined your model

>Misclassifications and uncertain results:  It's inevitable that even a well-trained machine learning model will sometimes misclassify its inputs. When you integrate a model into your application, you should take into account that it will not always give you the correct answer.


# 8. Deploying to your devices

Lastly, we need to extract this model to be source in our stm32 board.

Click on the Deployment, choose create library for `Cube.MX CMSI-PACK`, select optimization with EON (increase on-device performance but may reduce accuracy.), `Build` and Download the model.

![image](https://user-images.githubusercontent.com/57432755/122663698-15da5e00-d1cf-11eb-9afc-cf18dea009af.png)



Download [KWS_home_automation.1.0.1.pack](./KWS_home_automation.1.0.1.pack) before you continue to deploy into stm32 boards.

You can go [back to deploying this model into SMT32 board](../README.md)







