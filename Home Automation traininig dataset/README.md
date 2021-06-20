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

then, the features from MFCC will be feed to Neural Network blocks for modeling by using [keras network](https://www.tutorialspoint.com/keras/keras_introduction.htm). 



