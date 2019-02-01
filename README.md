# Technical issues regarding the digital room management for students at the library of the University of Siegen
This repository provides information about the technical issues regarding the digital room management for students and student groups at the library of the [University of Siegen](http://www.uni-siegen.de). Readers of this document are expected to have basic knowledge of web technologies.

## Introduction
The University of Siegen provides an [online service](https://www.ub.uni-siegen.de/reservierung/interim/) for students to book rooms in the library. These rooms are specially designed to provide a quit environment so that students can study for an exam or to work in general.
A student can either book a small room, or a larger room which is intended to be used by a group of students. In order to prevent misuse of these larger rooms, a booking of a larger room must be confirmed by another user. Thus a large room cannot by booked by a single person.  
Further the following restrictions hold:
- You can only book rooms if you are logged in (Using your library identification)
- A student can only book one room per day
- A large room reservation will be cancel the day before, if it does not get confirmed by another
- A full list of all the rules is provided on the online service website

## Issues
The main issue with this service is that most of the validation is done via client-side JavaScript
