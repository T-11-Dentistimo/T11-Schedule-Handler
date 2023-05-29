# **T11 - Schedule Handler**

## **Descripton**


This component is part of the Dentistimo system (more information [here](https://github.com/T-11-Dentistimo/T11---Project-documentation) and it is responsible for generating schedules for time intervals of interest using the opening hours of the clinics and the existing bookings.

It receives its inputs from the system's [web application](https://github.com/T-11-Dentistimo/T11-Web-Application) and the mongo cluster.

It keeps track of the monitored time intervals so it only publishes the necessary schedules to reduce the load on the broker. To do so, the component has a map containing the time intervals that are being watched by users and updates the map accordingly.
## **Responsibility**

- listen to schedule/request topic expecting a previous and a new interval in the message
- generate the schedule for the specified interval with the available

- publish the schedules to the schedule/response/\<interval>
- monitor for new booking insertions in the database and publish updated schedules to their respective topics

## **Data flow**

### **<ins>Input Data</ins>**
The input data are the clinics data, bookings and time interval. The intervals are stored in memory to keep track of which intervals are being watched by users. The component watches for insertion of bookings in the database and publishes updated schedules to the time intervals affected by the new booking. This monitoring is done via a change stream in the mongo db atlas cluster.

>Example Schedule Request
```
{
  "previousInterval": {
    "from": "2022-12-03",
    "to": "2022-12-10"
  },
  "newInterval": {
    "from": "2022-12-10",
    "to": "2022-12-17"
  }
}
```
>Example Initial Schedule request and Interval to be removed from the intervals map
```
  {
    "from": "2022-12-03",
    "to": "2022-12-10"
  }
```
### **<ins>Output Data</ins>**
Schedule containing the available slots for the requested time interval
>Example Schedule Response
```
[
  {
    "date": "2022-12-12",
    "schedule": {
      "6:00-6:30": [
        {
          "dentist": 3,
          "slots": 2
        }
      ],
      "6:30-7:00": [
        {
          "dentist": 3,
          "slots": 2
        }
      ],
      "7:00-7:30": [
        {
          "dentist": 2,
          "slots": 0
        },
        {
          "dentist": 3,
          "slots": 2
        }
      ],
      "7:30-8:00": [
        {
          "dentist": 2,
          "slots": 1
        },
        {
          "dentist": 3,
          "slots": 2
        }
      ],
      ...
```

## **Tools**

>  Eclipse Mosquitto broker <br>[Download here](https://mosquitto.org/download/)

>NodeJS <br>[Download here](https://nodejs.org/en/download/)

>Javascript IDE<br> Some alternatives: [Visual Studio Code](https://visualstudio.microsoft.com/downloads/) , [WebStorm](https://www.jetbrains.com/webstorm/download/)

### **<ins>SetUp</ins>**

| Description | Command/example |
|-------|---|
| Clone this repository | <ins>Option 1.</ins><br> Download as a zip file<br> <ins>Option 2.</ins><br>`git clone git@git.chalmers.se:courses/dit355/dit356-2022/t-11/t11-schedule-handler.git`|
| Open terminal and navigate to mosquitto root folder |  `mosquitto -c mosquitto.conf -v ` |
|Open the repo in javascript IDE and open the terminal in the IDE and install the dependencies| `npm install` |
|Create a .env file in the project root folder to store the atlas cluster uri in the MONGO_ATLAS_URI enviroment variable | MONGO_ATLAS_URI=\<altlas cluster URI> |
|Start the component | `npm run dev`











