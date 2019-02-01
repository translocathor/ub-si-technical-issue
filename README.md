# Technical issues regarding the digital room management for students at the library of the University of Siegen
This repository provides information about the technical issues regarding the digital room management for students and student groups at the library of the [University of Siegen](http://www.uni-siegen.de). Readers of this document are expected to have basic knowledge of web technologies.

## Disclaimer
The purpose of this document to inform the poeple who are responsible for information security and technical infrastructure at the University of Siegen. I don't want to encourage you to use this knowledge for malicious intentions.

## Introduction
The University of Siegen provides an [online service](https://www.ub.uni-siegen.de/reservierung/interim/) for students to book rooms in the library. These rooms are specially designed to provide a quit environment so that students can study for an exam or to work in general.
A student can either book a small room, or a larger room which is intended to be used by a group of students. In order to prevent misuse of these larger rooms, a booking of a larger room must be confirmed by another user. Thus a large room cannot by booked by a single person.  
Further the following restrictions hold:
- You can only book rooms if you are logged in (Using your library identification)
- A student can only book one room per day
- A large room reservation will be cancel the day before, if it does not get confirmed by another
- A full list of all the rules is provided on the online service website

## Issues
The main issue with this service is that most of the validation is done via client-side JavaScript. This leads to the problem that the server which is used to store and provide the booking data, will accept many commands to manipulate and access data, altough the user is not authorized to do that.  
By looking at the [main JavaScript file](https://www.ub.uni-siegen.de/reservierung/interim/js/functions.js) you can easily identify the function **bookIt** which books a romm. The following code listing shows the relevant 
```javascript
function bookIt(pre) {
    // pre b --> Reservierung mit Bestaetigung || pre s --> Reservierung ohne 2. Nutzer	
    var datum = $F(pre + "f-datum");
    var von = $F(pre + "f-von");
    var bis = $F(pre + "-bis");
    var raum = $F(pre + "f-raum");
    var form = 'booking-form';
    var suffix = '';
    var type = 'R';
    var keyterm = 'alert_marking_saved';
    if (pre == 'b') {
        var login_id2mix = $F("login_id2"); //fuer Siegen hinzugefuegt 13.10.2013
        var login_id2 = login_id2mix.toUpperCase(); //fuer Siegen hinzugefuegt 13.10.2013
        // ...
        var data = datum + "," + von + "," + bis + "," + raum + "," + type + "," + login_id2;
    } else {
        form = 'booking-form-single';
        suffix = '-single';
        type = 'S';
        var data = datum + "," + von + "," + bis + "," + raum + "," + type;
    }

    var url = "ajax_php/set_data.php";
    new Ajax.Request(url, {
        method: 'get',
        parameters: {
            action: "book_room",
            value: data
        },
        onSuccess: function(transport) {
            // ...
        }
    });
}
```
The code above shows that the actual data, which ich send to the server, is created by concatenating the date, start and end time, the room number, the room type and the ID of the seconds user (required for group room bookings). By simply analysing the code or using a modern browser's integrated web development tool, you can look at the full GET request which is sent to the server. For demonstration purposes the following section will provide an enumeration of actions that can be performed.
### Book a room
To book a room the file *ajax_php/set_data.php* is used with the following parameters:
- `action=book_room`
- `value=<DAY>,<START-TIME>,<END-TIME>,<ROOM-NUMBER>,<RESERVATION-TYPE>,<USER-2-ID>` 
Since the file *ajax_php/set_data.php* can handle different action, the desired actions is provided using the parameter `action`. The data which defines the booking is contained in the parameter `value`. The concatenated values in the `value` parameter are created in the following fashion:
- DAY: YYYYMMDD
- START-TIME: HHmm
- END-TIME: HHmm
- ROOM-NUMBER: 1, 2, 3, ... (Every room has a distinct number)
- RESERVATION-TYPE: 'S' for single rooms or 'R' for group rooms
- USER-2-ID: ID of the user who confirm bookings for rooms with RESERVATION-TYPE 'B'  
To actually book a room, the user needs to call *ajax_php/set_data.php* and send the data as described above. Below you can see an example of a request which books the group room number 2 on the 13. February of 2017 from 02:00 PM to 06:00 PM which needs to be confirmed by the user with ID *TEST*.
````
https://www.ub.uni-siegen.de/reservierung/interim/ajax_php/set_data.php?action=book_room&value=20170213%2C1400%2C1800%2C1%2CR%2CTEST
````
