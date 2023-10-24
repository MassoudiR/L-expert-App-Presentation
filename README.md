<h1 align="center">L'expert App Presentation</h1>
<h4 align="center">Introduction to the l'expert app, how it works and its purpose</h4>

<center  >

<p align="center" style="display: flex; justify-content: center;">
  <img src="https://github.com/MassoudiR/L-expert-App-Presentation/blob/main/Assets/Logo%20L'expert%20School.png?raw=true" alt="Logo L'expert School" style="width: 35%;max-width: 50%;">
  <img src="https://github.com/MassoudiR/L-expert-App-Presentation/blob/main/Assets/img.png?raw=true" alt="Logo App" style="width: 45%;max-width: 50%;">
</p>
</center>

## Purpose of the application
### Scenario : 
The L'expert app serves as an integral part of establishing a comprehensive management platform for a private school. In response to the client's specific requirements, the application has been designed to facilitate various communication functionalities directly from the platform, without the need for physical handling of the device.

The client emphasized the necessity for the platform to send SMS messages to students and administrators and to enable phone call functionalities, including the ability to both make and answer calls through the platform. Despite suggested alternatives such as SIP services and SMS services like Twilio and SendGrid, the client insisted on utilizing the institution's SIM card to avoid monthly service costs.

In line with this requirement, the application allows the integration of the phone with the web server, enabling the following services directly from the platform:

- Sending SMS messages 
- Making phone calls
- Answering calls
- Rejecting calls

However, it's important to note that the application does not provide online voice broadcasting services. Therefore, it is essential to connect the phone with a headset for this purpose.
> [!IMPORTANT] 
Moreover, to ensure the protection of phone numbers and callers' identities, the application was developed following kiosk mode standards. **It is mandatory to set a password to exit the application**  , and the default phone interface is blocked to maintain the confidentiality and security of the communication process.

This approach guarantees the seamless integration of communication services within the school's management platform while prioritizing data security and cost-efficiency.

## Documentation

To get started with the L'expert App, follow these steps:

#### 1. Install the Flutter Application

You can install the Flutter application from the [Releases section](https://github.com/MassoudiR/L-expert-App-Presentation/releases/download/apk/L.expert.School.apk) of this repository. Download the appropriate version for your Android device. Make sure to grant the necessary permissions during the installation process.

#### 2. Server preparation

**Note**: The platform was designed with a Django framework; however, you have the flexibility to choose any other framework and incorporate the application based on the application's workflow, as described in this section.

### Getting Started
To incorporate the Django SIP Services app into your Django project, follow these steps:
1. Ensure you have an existing Django project set up. If not, create one following Django's official documentation.

2. Navigate to your Django project's directory.

3. Download the `sip-services-app` from this repository. 

4. Extract the contents of the `sip-services-app` into your Django project directory.
```
your_django_project/
│
├─ sip_services-app/
│   ├── admin.py
│   ├── apps.py
│   ├── migrations/
│   ├── models.py
│   ├── consumers.py
│   ├── views.py
│   └── ...
│
├─ your_app1/
│   ├── ...
│
├─ your_app2/
│   ├── ...
│
├─ manage.py
├─ your_project_settings.py
└─ ...
```

5. In your Django project's `settings.py` file, add `'sip_services-app'` to the `INSTALLED_APPS` list.

```python
   INSTALLED_APPS = [
       # ...
       'sip_services',
       # ...
   ]
```

6. Add the ASGI configuration for WebSocket support to your project. Create a file named asgi.py in your project's root directory and add the following code:

```python

import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from sip_services_app.routing import websocket_urlpatterns
import django

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'Core.settings')
django.setup()

application = ProtocolTypeRouter({
    'http': get_asgi_application(),
    'websocket': URLRouter(websocket_urlpatterns),
})
```
7. Now, let's set up the necessary URLs. In your Django project's urls.py, include the following:

```python

from django.urls import path, include

urlpatterns = [
    # Your existing URL patterns
    path('sip_services/', include('sip_services.urls', namespace='sip_services')),
    # Add the SIP Services app URL patterns
]
```
This step enables the SIP Services app's URLs to work within your Django project.

8. Run the following command to apply the migrations for the SIP Services app:

```bash
python manage.py migrate sip_services-app
```
9. Finally, run your Django project by executing the following command:

```bash
python manage.py runserver
```
### Configure the front end

#### Finally, configure the front end of your application:
  .  Include the sip-app file in the templates directory of your project.
  .  Include the sip.app.js file in the static directory.

```
your_project/
├── manage.py
├── your_app/
│   ├── ...
├── sip_services/
│   ├── ...
└── your_project/
    ├── templates/
    │   └── sip-app
    │       ├── index.html
    └── static/
        └── sip/
            ├── sip.app.js
```

## Overview

### From 📱 (mobile) to 🖥️ (server) 
- Phone status: connected or not 
  ```json
  {"action": "phone_status", "data": connected}
  ```
  The type of the variable 'connected' : True Or False

- calls
  ```json
  {
      "action": "CALL_STARTED",
      "data": {
          "number": "56646971",
          "name": "Med Rayen"
      }
  }
  ```
  The type of the variable 'action' : 
  
  ```diff
  # CALL_INCOMING : The phone rings
  # CALL_STARTED : Start the call
  # CALL_ENDED : The call ends
  ```
- SMS
  ```json
  {
      "action": "SMS",
      "data": {
          "number": "56646971",
          "name": "Med Rayen",
          "Content": "Example SMS message",
          "Type": "Receiving"
      }
  }
  ```
  The type of the variable 'Type' :  Sends Or Receiving

### From 🖥️ (server) to 📱 (mobile) 

- Make a phone call
  
  ```
  {
    action: 'Call',
    data: {
      '+21612345678',
    },
  };
  ```
  > [!NOTE]
  > The js interface provides ready-made functions to handle calls
  ```js
  makeCallToServer(number)
  makeCallUiScreen()
  ```
- Call control
  ```
  {
    action: 'Answer',
    data: {},
  }
  ```
  'action' must be one of two values
   ```diff
  # Answer : To answer the call
  # Reject : To Reject the call
  ```
- SMS
  ```
  {
    action: 'Send',
    data: {
      message,
      numbers,
      },
  }
  ```
  'action' must be one of two values
  ```
    1- When sending an SMS message, its content must not exceed 160 characters, taking into account spaces
    2- Phone numbers must be of type str in  array
  ```
    ```js
    numbers = ['2666655',...]
    ```

> [!NOTE]
> The js interface in the sip.js file provides all the functions to deal with the application for the user or programmatically and is customizable. These are some of the ready-made functions
```js
/// (Programmatically)
sendMessage(message, numbers)
makeCall(number)
answerCall()
reject()

/// GUI

phoneConnectionFab()
IncomingCallScreenUI()
inCallScreenUI()
EndedcallScreenUI()
SendSmsUI()
MakeCallUI()
...


```
    
## Screenshots

https://github.com/MassoudiR/L-expert-App-Presentation/assets/20273581/ac02c31c-aa3c-4b9b-bea7-7f6c19a2d8b4


 
   <center  >

<p align="center" style="display: flex; justify-content: center;">
  <img src="https://github.com/MassoudiR/L-expert-App-Presentation/blob/main/Assets/screenshotWeb.gif?raw=true" alt="Logo L'expert School" style="width: 100%;max-width: 100%;">
  <img src="https://github.com/MassoudiR/L-expert-App-Presentation/blob/main/Assets/screenshotApp.jpg?raw=true" alt="Logo App" style="width: 45%;max-width: 50%;">
</p>
</center>   


  
  
  


  
  
  







