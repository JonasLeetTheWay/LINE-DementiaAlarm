# LINE Chatbot - Voice Memo Alarm

A new way to send voice reminders through LINE. Want to remind someone something? Start with LINE.

Your voice message in LINE can be directly broadcasted to the speaker, which can be heard easily. It comes with alarm feature, so your voice message will be automatically broadcasted at the right time. Note that, the alarm is customizable voice message... Now that is revolutionary!

> Mobile-only: Some features are not available on LINE desktop app

## Tech Stack
![Node.js](https://img.shields.io/badge/Node.js-14+-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black)
![LINE](https://img.shields.io/badge/LINE-00C300?style=for-the-badge&logo=line&logoColor=white)
![Google Cloud](https://img.shields.io/badge/Google_Cloud-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Cloud Functions](https://img.shields.io/badge/Cloud_Functions-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Cloud Tasks](https://img.shields.io/badge/Cloud_Tasks-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Firestore](https://img.shields.io/badge/Firestore-FFCA28?style=for-the-badge&logo=firebase&logoColor=black)

### Key Technologies
- **Backend**: Node.js, Firebase Cloud Functions
- **Database**: Firestore (NoSQL)
- **Messaging**: LINE Messaging API
- **Cloud Services**: Google Cloud Tasks, Firebase Storage
- **Architecture**: Serverless, Event-driven
- **State Management**: Custom State Machine Implementation
- **UI/UX**: LINE Flex Messages, Rich Menu

### Technical Implementation
The integration between LINE and Firebase was achieved through:

1. **Webhook Architecture**
   - LINE events trigger Firebase functions
   - Real-time processing of user interactions
   - Seamless state transitions

2. **Message Templates**
   - Reusable components for common interactions
   - Consistent user experience
   - Easy maintenance and updates

3. **State Management**
   - Custom state machine implementation
   - Context-aware responses
   - Error handling and recovery

## Table of Contents
1. [System Architecture](#system-architecture)
2. [Project Structure](#project-structure)
3. [User Interface](#user-interface)
4. [Tutorials](#tutorials)

## System Architecture

### Class Diagram
```mermaid
classDiagram
    class BaseDbUserChatBot {
        +belongTo
        +db
        +alarmCounter
        +name
        +topLevelData
        +subData
        +translator
        +randomPopulateDB()
        +generateQuickRepliesAsync()
        +getSortedAlarmsDataAsync()
        +abort()
        +addQuickReply()
        +reply()
        +reactTextAsync()
        +reactAudioAsync()
        +reactPostbackAsync()
    }

    class DefaultChatBot {
        +reactTextAsync()
        +reactAudioAsync()
        +reactPostbackAsync()
    }

    class AlarmBase {
        +acquireAlarmId()
        +replyUntilAlarm()
        +alarmOneAsync()
        +alarmAllAsync()
    }

    class AlarmDeleter {
        +reactPostbackAsync()
    }

    class AlarmWatcher {
        +reactPostbackAsync()
    }

    class AlarmSetter {
        +reactPostbackAsync()
        +setAudio()
        +loadEditWatch()
    }

    class LangSelector {
        +reactPostbackAsync()
        +changeLang()
    }

    class DbUser {
        +db
        +translator
        +chatbot
        +setHolder()
        +parseDatetime()
        +save()
        +replyMessage()
        +onText()
        +onAudio()
        +onPostback()
        +init()
        +startProcessing()
    }

    BaseDbUserChatBot <|-- DefaultChatBot
    BaseDbUserChatBot <|-- AlarmBase
    AlarmBase <|-- AlarmDeleter
    AlarmBase <|-- AlarmWatcher
    AlarmBase <|-- AlarmSetter
    BaseDbUserChatBot <|-- LangSelector
```

### State Machine
```mermaid
stateDiagram-v2
    [*] --> DefaultState
    DefaultState --> AlarmSetting: Set Alarm
    DefaultState --> AlarmWatching: View Alarms
    DefaultState --> AlarmDeleting: Delete Alarm
    DefaultState --> LanguageSetting: Change Language
    
    AlarmSetting --> DefaultState: Complete/Cancel
    AlarmWatching --> DefaultState: Back
    AlarmDeleting --> DefaultState: Complete/Cancel
    LanguageSetting --> DefaultState: Complete
```

### Activity Flows
```mermaid
sequenceDiagram
    participant User
    participant LINE
    participant Chatbot
    participant Firebase
    participant CloudTasks

    User->>LINE: Send Message
    LINE->>Chatbot: Webhook Event
    Chatbot->>Firebase: Get User Data
    Chatbot->>Chatbot: Process Command
    alt Set Alarm
        Chatbot->>Firebase: Save Alarm
        Chatbot->>CloudTasks: Schedule Task
    else View Alarms
        Chatbot->>Firebase: Query Alarms
        Chatbot->>User: Display Alarms
    else Delete Alarm
        Chatbot->>Firebase: Remove Alarm
        Chatbot->>CloudTasks: Cancel Task
    end
    Chatbot->>LINE: Send Response
    LINE->>User: Receive Message
```

## Project Structure

### Serverless Backend
The backend is built on Firebase for its reliability and scalability. Key features include:
- Real-time database with Firestore
- Serverless functions for handling LINE events
- Cloud Tasks for scheduling alarms

### API Integration
- LINE Messaging API for user interaction
- Firebase for backend services
- Cloud Tasks for scheduling

## User Interface

### Menu
<img src="https://user-images.githubusercontent.com/75115433/201467962-dfc08c4c-a24d-40b6-a382-7ad4fe81854d.jpeg" width=350>

### Floating Button
<img src="https://user-images.githubusercontent.com/75115433/201467648-c9bb3b5f-f7ac-4e15-bb3c-3cf31b13ee1f.png" width=350>
<br>
<img src="https://user-images.githubusercontent.com/75115433/201467649-bb4c953d-1a29-4449-8291-b543ceaf700c.jpg" width=350>
<br>
<img src="https://user-images.githubusercontent.com/75115433/201467968-bfb08d3f-5137-47de-9fc2-5106a88802aa.jpeg" width=350>
<br>
<img src="https://user-images.githubusercontent.com/75115433/201468430-797d7639-6a1e-4219-b3a9-ddb7b2338488.jpg" width=350>

### Alarm Info Display
<img src="https://user-images.githubusercontent.com/75115433/201467644-9e5cb6c8-48b2-4db7-9868-0b66b9c9d22a.jpg" width=350>
<br>
<img src="https://user-images.githubusercontent.com/75115433/201467646-8143a2f2-eef9-464b-b337-571f789941e5.png" width=350>
<br>
<img src="https://user-images.githubusercontent.com/75115433/201467647-61279def-73e1-46dd-890e-dfc8be705437.png" width=350>

### Terminology
|<img src="https://user-images.githubusercontent.com/75115433/201294998-3a7157ff-0238-4150-8ba5-2a08d8bf5870.png" width="70"/> |<img src="https://user-images.githubusercontent.com/75115433/201294976-fd948a9f-0465-43f2-904e-fb37d13e3b66.png" width="70"/>|<img src="https://user-images.githubusercontent.com/75115433/201469188-fd4d276b-493c-4fcc-996d-b489257caa50.png" width="70"/>|
|:--------:|:--------:|:--------:|
|Alarm Watcher|Alarm Deleter|Task Manager|

## Tutorials
> *there are many* ► *buttons below, press them to watch tutorial video*

#### Language Selection
|          |**Procedures**|
|:--------:|:-----------------    |
|| 1. type in "lang" |
|| 2. navigate floating button |
|*how to...*| <details><summary>select language</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201319528-bf8d4167-d18c-4ce3-bb89-2aa80fc91515.gif" width="250"/> </p></details>|

#### Voice Alarm Setup
|          |**Procedures**|
|:--------:|:-----------------    |
|| 1. record voice message to start |
|| 2. pick a datetime for your voice alarm |
|| 3. received details about the alarm you just set|
|*how to...*| <details><summary>record</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201287327-78389f5c-90a0-4f83-a563-c55163c43a50.gif" width="250"/> </p></details>|

#### Viewing Alarms
##### After Recording
||**Procedures**|
|:-:|:--------------------            |
||navigate floating button |
|*how to...*|<details><summary>view</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201311281-80f27a2f-33a2-4e89-8bc7-a08b2dfb493e.gif" width="250"/> </p></details> |

##### Using AlarmWatcher
||**Procedures**|
|:-:|:--------------------            |
||navigate floating button |
|*how to...*|<details><summary>view all</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201305840-256eb03b-add3-4fb7-a418-40a1c9a847ca.gif" width="250"/> </p></details> |
|*how to...*|<details><summary>view single</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201311272-acf88cc9-9f11-485b-9c92-662af1d16f89.gif" width="250"/> </p></details>|

#### Managing Alarms
||**Procedures**|
|:--:|:--------------------            |
||1. navigate floating button |
||2. <li> press "Edit" to reassign timer for the voice alarm </li> <li>press "View" to listen to recorded voice alarm </li>|
|*how to...*|<details><summary>reconfigure</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201287338-0f075bff-287f-4eb9-99d2-1f1d23af15d7.gif" width="250"/> </p></details> |

#### Deleting Alarms
##### After Recording
||**Procedures**|
|:--:|:----------------- |
||press "Cancel" just after recording voice alarm <li>delete only that particular voice message you just recorded</li> |
|*how to...*| <details><summary>cancel</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201305918-d807419b-5be8-4100-b79a-c22cc3187603.gif" width="250"/>   </p></details> |

##### Using AlarmDeleter
||**Procedures**|
|:---|:----------------- |
||press "Delete Alarms" icon on Richmenu <li> delete any alarms of your choice </li><li> delete all alarms  </li> |
|*how to...*| <details><summary>delete</summary><p><img src="https://user-images.githubusercontent.com/75115433/201305821-6eb11701-9aaf-4782-aeb0-db792c1cdb87.gif" width="250"/>  </p></details> |
|*how to...*| <details><summary>exit</summary><p> <img src="https://user-images.githubusercontent.com/75115433/201305833-c55cd141-b06a-4c73-a270-86387ac17679.gif" width="250"/>  </p></details> |
