# Ambulance-Route-Optimization-Problem

# Ambulance Dispatch & Route Optimization System

A full-stack emergency ambulance dispatch and tracking system that helps dispatchers identify suitable ambulances and hospitals based on availability, medical specialty, and estimated travel time.

The system provides separate interfaces for dispatchers/users, ambulance drivers, and administrators, with real-time ambulance location updates and map-based route visualization.

## Features

### Dispatcher / User Module

* User signup and login
* Enter patient location
* Select required medical specialty
* Find suitable available ambulances
* Select an appropriate hospital based on specialty and availability
* View ambulance route and estimated travel time
* Monitor active ambulance dispatches
* View dispatch history

### Ambulance Driver Module

* Driver standby interface
* Receive ambulance dispatch requests
* Send current GPS/location information
* Real-time route and location updates
* Mark patient as picked up
* Navigate from patient location to the selected hospital
* Complete the ambulance mission

### Admin Module

* Admin authentication
* Monitor ambulance fleet status
* View hospital information and availability
* Monitor active dispatches
* View dispatch history
* View operational statistics

## Route Selection

The backend integrates the **TomTom Routing API** for geocoding and route calculations.

The ambulance selection process works as follows:

1. Receive the patient's location and required medical specialty.
2. Convert the patient's location into coordinates when required.
3. Retrieve ambulances currently marked as `available`.
4. Calculate the travel time from each available ambulance to the patient.
5. Compare the estimated travel times.
6. Select the ambulance with the minimum estimated travel time.
7. Find hospitals that support the required medical specialty.
8. Filter hospitals based on bed availability.
9. Calculate travel time from the patient to each eligible hospital.
10. Select the suitable hospital with the lowest estimated travel time.

### Simplified Workflow

```text
Patient Request
      |
      v
Patient Location + Medical Specialty
      |
      v
Find Available Ambulances
      |
      v
TomTom Route / ETA Calculation
      |
      v
Select Minimum-ETA Ambulance
      |
      v
Find Eligible Hospitals
      |
      v
Check Specialty + Bed Availability
      |
      v
Calculate Hospital ETA
      |
      v
Select Suitable Hospital
      |
      v
Dispatch Ambulance
```

## Real-Time Ambulance Tracking

The system uses **Flask-SocketIO** on the backend and **Socket.IO Client** on the frontend for real-time communication.

The ambulance driver's location is continuously sent to the backend. The backend processes the updated location and sends route information to the relevant dispatcher/client.

```text
Ambulance Driver
       |
       | GPS / Location Update
       v
Socket.IO
       |
       v
Flask Backend
       |
       | Route / ETA Update
       v
Dispatcher Dashboard
       |
       v
Live Map
```

When the driver picks up the patient, the active route changes from:

```text
Ambulance → Patient
```

to:

```text
Patient → Hospital
```

## Map and Route Visualization

The frontend uses **React-Leaflet** and **Leaflet** to display the ambulance operation on an interactive map.

The map can represent:

* Ambulance location
* Patient location
* Hospital location
* Calculated route
* Updated ambulance position

## Technology Stack

### Frontend

* React
* Vite
* React-Leaflet
* Leaflet
* Socket.IO Client
* JavaScript
* HTML
* CSS

### Backend

* Python
* Flask
* Flask-CORS
* Flask-SocketIO
* Eventlet
* Requests

### Database

* MongoDB
* PyMongo

### External Services

* TomTom Routing API
* TomTom Geocoding API
* OpenStreetMap / Leaflet map visualization

## Project Structure

```text
Ambulance-Route-Optimization-Problem/
│
├── backend/
│   ├── app.py
│   ├── auth.py
│   └── db.py
│
├── frontend/
│   └── Trailz-react/
│       ├── src/
│       │   ├── Admin.jsx
│       │   ├── AdminDashboard.jsx
│       │   ├── AdminLogin.jsx
│       │   ├── AmbulanceDispatch.jsx
│       │   ├── Dashboard.jsx
│       │   ├── Dispatcher.jsx
│       │   ├── DriverStandby.jsx
│       │   ├── DriverView.jsx
│       │   ├── History.jsx
│       │   ├── LeafletMap.jsx
│       │   ├── Login.jsx
│       │   ├── Signup.jsx
│       │   ├── api.js
│       │   ├── socket.js
│       │   └── main.jsx
│       │
│       ├── package.json
│       └── vite.config.js
│
└── README.md
```

## Database Collections

The backend uses MongoDB collections for application data, including:

```text
users
hospitals
ambulances
dispatches
```

### Ambulances

Stores information such as:

* Ambulance unit
* Location
* Latitude
* Longitude
* Current status

Example statuses include:

```text
available
enroute
```

### Hospitals

Stores:

* Hospital name
* Location
* Latitude
* Longitude
* Medical specialties
* Bed availability

### Dispatches

Stores completed dispatch information such as:

* Dispatcher/user information
* Patient location
* Selected ambulance
* Selected hospital
* Dispatch information
* Time-related information

## Authentication

The application provides authentication functionality for users and administrators.

User passwords are handled using password hashing and verification through Werkzeug security utilities.

## Running the Project

### 1. Clone the repository

```bash
git clone <your-github-repository-url>
cd Ambulance-Route-Optimization-Problem
```

### 2. Backend Setup

Navigate to the backend:

```bash
cd backend
```

Install the required Python packages:

```bash
pip install flask flask-cors flask-socketio eventlet pymongo requests werkzeug
```

Make sure MongoDB is available and configure the required database/API settings.

Run the Flask server:

```bash
python app.py
```

### 3. Frontend Setup

Open another terminal:

```bash
cd frontend/Trailz-react
```

Install dependencies:

```bash
npm install
```

Start the React development server:

```bash
npm run dev
```

The frontend will then be available through the local Vite development server.

## Main Application Routes

### Ambulance Driver

```text
/driver-standby
```

Used by ambulance drivers to access the standby/driver interface.

### Admin

```text
/admin
```

Used to access the administration interface.

## System Workflow

```text
                     ┌─────────────────────┐
                     │ Dispatcher / User   │
                     └──────────┬──────────┘
                                │
                       Patient Request
                                │
                                v
                     ┌─────────────────────┐
                     │   Flask Backend     │
                     └──────────┬──────────┘
                                │
               ┌────────────────┼────────────────┐
               │                │                │
               v                v                v
          MongoDB          TomTom API       Socket.IO
               │                │                │
               │          Route / ETA            │
               │                │                │
               └────────────────┼────────────────┘
                                │
                                v
                     Ambulance Assignment
                                │
                                v
                     ┌─────────────────────┐
                     │  Ambulance Driver   │
                     └──────────┬──────────┘
                                │
                       Live Location
                                │
                                v
                           Socket.IO
                                │
                                v
                     ┌─────────────────────┐
                     │ Dispatcher Dashboard│
                     │   Live Map / ETA    │
                     └─────────────────────┘
```

## Future Improvements

Potential improvements for a production deployment include:

* Store API keys and credentials in environment variables
* Implement stronger role-based authorization
* Add JWT/session-based authentication
* Add stricter CORS configuration
* Add input validation and rate limiting
* Implement atomic ambulance reservation for simultaneous dispatch requests
* Record actual dispatch, pickup, and completion timestamps
* Improve failure handling when routing or database services are unavailable
* Add notification services for emergency dispatch updates
* Deploy the frontend and backend using a cloud platform

## Project Purpose

The project demonstrates how a full-stack application can combine:

* Web application development
* REST APIs
* MongoDB database management
* External routing API integration
* GPS/location data
* Real-time communication
* Interactive maps
* Emergency dispatch workflows

to support an ambulance dispatch and tracking workflow.

## Author

**Gnanaprakash MS**

B.Tech Information Technology
Sri Sivasubramaniya Nadar College of Engineering

---
