# WatsonAssistant-VoiceChatbot-Flutter

In this project I'm using IBM Watson Assistant integrated in a Flutter application to create a voice chatbot, this chatbot is a customer service bot for [smart methods](https://s-m.com.sa/en.html).

## Before we start let's have a quick chat !
Just click on the mic icon when you want to speak and smarty will automatically detect when you are done speaking and replay back :)

https://user-images.githubusercontent.com/85634099/140885518-11acdf9f-440d-41ce-964f-677ffd63ee19.mp4



## Chatbot Flow
This project has multiple stages to go through so make sure each step is working before moving to the next, Start by creating your Flutter app then follow these steps in order to create the voice chat:
1) Convert user's speech to text.
2) Pass the text from (step 1) to watson assistant and get the chatbot's response.
3) Convert the chatbot's response in (step 2) from text to speech.


## A) Speech To Text
As this is my first flutter app ever, I decided not to start from scratch, and instead, use the code from [Johannes Milke](https://github.com/JohannesMilke/speech_to_text_example) which turned out very helpful after following the [Tutorial](https://www.youtube.com/watch?v=jwlgHLHFIjc). Note that his project has other functionalities like sending email and opening google which i currently don't need so i ended up using the code of these three files:
* main.dart: for running the app.
* home_page.dart: defining the page visuals & record button functionality. 
* speech_api.dart: for both listening and not listening states & getting what the user said.

And to use these pages you'll need the following packages in your pubspec.yaml file:
```
  permission_handler: ^8.1.2
  speech_to_text: ^4.2.2
  avatar_glow: ^2.0.1
  ibm_watson_assistant: ^1.1.2
  flutter_tts: ^3.2.2
  sentry_flutter: ^5.1.0
  flutter_native_splash: ^1.2.1
  flutter_launcher_icons: ^0.9.1
```

Also , add these lines to your AndroidManifest.xml to ask the user for permission to use the internet and record the audio:
```
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.RECORD_AUDIO"/>
```

Don't forget to make sure that your minSdkVersion is not less than 21, otherwise you'll run into errors, to check that go to: 

your project >> android >> app >> build.gradle

```
defaultConfig {
        applicationId "com.example.smarty_chatbot"
        minSdkVersion 21
        targetSdkVersion 30
        versionCode flutterVersionCode.toInteger()
        versionName flutterVersionName
    }
```

Now run the app,it will asks for record permission then you should have a screen similar to this, press the mic to start talking

*Note : After this step you should make sure that your speech to text is working fine otherwise don't move to the next step.*

![image](https://user-images.githubusercontent.com/85634099/128926153-5e374736-08c2-47dd-b0b4-7710a83b5cb4.png)


## B) Watson Assistant
To continue with this step you need to have your assistant ready, if you don't have it yet check [IBM Watson](https://github.com/ReemALHamed/Synchronization-embedded-chatbot-technique-using-IBM-Watson) for how to build your own watson assistant.

My chatbot is a customer service bot for [smart methods](https://s-m.com.sa/en.html). so I created the intents based on the data from their website (ex: robots, contact info ... etc) so the chatbot can answer any company related questions in addition to other intents so the :

![image](https://user-images.githubusercontent.com/85634099/128924568-95a37693-9db1-4ccf-a684-32f0767266eb.png)


### Assistant's Credentials
To connect your assistant to your code , add the following lines to the (home_page.dart) page , these line creates an authentication object of the IbmWatsonAssistant: 
```
final auth = IbmWatsonAssistantAuth(
    assistantId: 'AssistantID',
    url: 'URL,
    apikey: 'APIKEY',
  );
```
These previous few lines require the assistant's credentials , so go to your assistant >> settings >> get Assistant ID & Assistant URL & API Key , and paste them into your code

![image](https://user-images.githubusercontent.com/85634099/128914149-b1846795-e5f2-4a88-84f8-f1253406ff7d.png)

### Assistant's Object & Response
Now create the Watson Assistant object and pass it's authentication, create the session and send the user speech after being converted to text in (step A) through variable named Text, and finally get the chatbot Text response:
```
final bot = IbmWatsonAssistant(auth);
final sessionId = await bot.createSession();
var botRes = await bot.sendInput(text, sessionId: sessionId);
String robotresponse=  botRes.responseText;
```
*Note: to check if this part works correctly use print(robotresponse) to see the robot response*



## C) Text To Speech
Now it's the chatbot turn to talk, Start by creating an object of Flutter Text To Speech (FlutterTts), then set the language, pitch and spped rate:
```
var tts = FlutterTts();
  Home() {
    tts.setLanguage('en');
    tts.setPitch(0);
    tts.setSpeechRate(0.4);
  }
```
And for the chatbot to offically talk, you have to pass the chatbot text reponse from (step B) through the function speak:
```
tts.speak(robotresponse);
```



#### Now everything should works perfectly !!
 
## Design
This step is optional but what would be more perfect than a talking robot? 

A HANDSOME TALKING ROBOT !! 
May I present to you ... SMARTY !! the smart robot :D 


I used [Canva](https://www.canva.com/ar_eg/q/pro/?v=2&utm_source=google_sem&utm_medium=cpc&utm_campaign=sa_ar_all_pro_rev_conversion_branded-tier1-core_em&utm_term=sa_ar_all_pro_rev_conversion_branded-tier1_Canva_EM) to design the robot expressions (ex: happy, sad, shy ..etc) 
|              Innocent             |             Sad             |             Shy           |
| :----------------------------------: | :----------------------------------: |:----------------------------------: |
| <img src="https://user-images.githubusercontent.com/85634099/128929938-8b7b2256-36b7-4f7c-84ef-8a086ed659be.gif" width="350">|<img src="https://user-images.githubusercontent.com/85634099/128930145-c6d7bc5f-9d2c-4462-b641-8126a536c352.gif" width="350">|<img src="https://user-images.githubusercontent.com/85634099/128929626-2ae771da-387f-4e22-b9e9-983e30072cbe.gif" width="350">|

And there are more expressions that appears according to the speech intents, to implement this part in my project I extracted the intent from the bot response and pass it through a switch statement with each case representing an intent & changing the display gif's path to the corresponding expression:
```
final bot = IbmWatsonAssistant(auth);
      final sessionId = await bot.createSession();
      var botRes = await bot.sendInput("ok", sessionId: sessionId);
      res=json.encode(botRes);
      Map<String, dynamic> user = jsonDecode(res);
      category = user['output']['intents'][0]['intent'];
      robotresponse=botRes.responseText!;
      tts.speak(robotresponse);
    
    switch(category) {
   
      case "location": {robot = 'assets/images/locations.gif';}
      break;

      case "contactus": {robot = 'assets/images/contact.gif';}
      break;

      case "General_Negative_Feedback": {robot = 'assets/images/sad.gif';}
      break;

      case "General_Positive_Feedback": {robot = 'assets/images/love.gif';}
      break;
      }
```

## Final Result
After I added the design I run the chatbot and start chatting, i had a small chat but you can try run my project and have a longer conversations with 17 intents in the bot, you can ask:
* about the company
* company achievements
* ambassador
* company location
* contact info
* are you human or robot
* what is you name
* what do you do
* company clients
* company robots




## Final Touches
I added an icon for the app and changed the app name to 'Smarty' , i also added [flutter_native_splash](https://pub.dev/packages/flutter_native_splash) screen to launch once the app runs.

### App Icon
![image](https://user-images.githubusercontent.com/85634099/128949680-65aa56bf-c438-41bf-b15c-9e1d4d57c999.png)

### Fultter Native Splash
https://user-images.githubusercontent.com/85634099/128949543-6fdf354f-8996-49ae-9f0f-76fa49de3510.mp4



