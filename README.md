+ Manual de Desarrollo: Sistema de Rastreo de Transporte Escolar (BusTracker1)
+ -1. Introducción y Requisitos del Sistema
Este proyecto consiste en una aplicación Android nativa desarrollada en Java que permite la gestión y rastreo de transporte escolar en tiempo real. El sistema conecta tres roles principales:

Administrador: Gestiona conductores, alumnos y rutas.

Conductor: Comparte su ubicación en tiempo real y envía reportes de incidencias.

Pasajero (Estudiante/Padre): Visualiza la ubicación del autobús y recibe notificaciones.

Requisitos Previos
Android Studio: Versión Iguana o superior (recomendado).

JDK: Java Development Kit 1.8 (Java 8).

Cuenta de Firebase: Para base de datos en tiempo real y autenticación.

Cuenta de Google Cloud Console: Para obtener la API Key de Google Maps.

2. Configuración del Proyecto y Dependencias
Esta sección detalla los archivos de configuración necesarios para que la aplicación compile y se conecte a los servicios externos.

A. Configuración de Construcción (Gradle)
El archivo build.gradle (Module: app) define las bibliotecas externas. Es crucial incluir las librerías de Firebase (Auth, Database) y Google Play Services (Maps, Location).

Ubicación: Gradle Scripts > build.gradle (Module: app)

Función: Descargar e integrar las herramientas de Google y componentes de diseño visual.

Groovy

plugins {
    id("com.android.application")
    id("com.google.gms.google-services")
    id 'com.google.android.libraries.mapsplatform.secrets-gradle-plugin'
}

android {
    namespace 'com.example.bustracker1' // Identificador único de la app
    compileSdk 34 // Versión de compilación Android 14

    defaultConfig {
        applicationId "com.example.bustracker1"
        minSdk 23 // Android 6.0 (Marshmallow) mínimo
        targetSdk 34
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    buildFeatures {
        viewBinding true // Permite una vinculación más segura con las vistas XML
    }
}

dependencies {
    // Plataforma Firebase (Bill of Materials) para gestionar versiones
    implementation(platform("com.google.firebase:firebase-bom:34.6.0"))
    
    // Componentes visuales y de sistema
    implementation("com.google.firebase:firebase-analytics")
    implementation 'androidx.appcompat:appcompat:1.4.2'
    implementation 'com.google.android.material:material:1.6.1' // Material Design (CardView, Inputs)
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    
    // Servicios Backend (Firebase)
    implementation 'com.google.firebase:firebase-auth:21.0.6' // Inicio de sesión
    implementation 'com.google.firebase:firebase-database:20.0.5' // Base de datos Realtime
    
    // Servicios de Mapas y Ubicación
    implementation 'com.google.android.gms:play-services-maps:18.1.0'
    implementation 'com.google.android.gms:play-services-location:20.0.0'
    
    // Pruebas unitarias
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
}
B. Manifiesto de la Aplicación (AndroidManifest)
El manifiesto es el "carnet de identidad" de la app. Aquí se declaran los permisos sensibles (Ubicación, Internet, Notificaciones) y se registran todas las pantallas (Activities).

Ubicación: app/src/main/AndroidManifest.xml

Función: Solicitar permisos al sistema operativo y definir la pantalla de inicio (splashscreen).

XML

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.INTERNET" /> <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" /> <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" /> <uses-permission android:name="android.permission.POST_NOTIFICATIONS" /> <uses-permission android:name="android.permission.VIBRATE" /> <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@drawable/schoolbus"
        android:label="@string/app_name"
        android:roundIcon="@drawable/schoolbus"
        android:supportsRtl="true"
        android:theme="@style/Theme.Bustracker1"
        tools:targetApi="31">

        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="TU_API_KEY_AQUI" /> 
            <activity
            android:name="com.example.bustracker1.splashscreen"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity android:name="com.example.bustracker1.adminlogin" android:exported="false" />
        <activity android:name="com.example.bustracker1.createadminacc" android:exported="false" />
        <activity android:name="com.example.bustracker1.admin" android:exported="false" />
        <activity android:name="com.example.bustracker1.Admin_add_driver" android:exported="false" />
        <activity android:name="com.example.bustracker1.admindriverpage" android:exported="false" />
        <activity android:name="com.example.bustracker1.adminPasajeropage" android:exported="false" />
        <activity android:name="com.example.bustracker1.editPasajerodetails" android:exported="false" />
        <activity android:name="com.example.bustracker1.AdminChooseDriverActivity" android:exported="false" />
        <activity android:name="com.example.bustracker1.AdminAddStopActivity" />

        <activity android:name="com.example.bustracker1.DriverLogin" android:exported="false" />
        <activity android:name="com.example.bustracker1.driverpage" android:exported="false" />
        <activity android:name="com.example.bustracker1.DriverLocation" android:exported="false" />
        <activity android:name="com.example.bustracker1.DriverReportActivity" android:exported="false" />

        <activity android:name="com.example.bustracker1.login" android:exported="false" />
        <activity android:name="com.example.bustracker1.createacc" android:exported="false" />
        <activity android:name="com.example.bustracker1.setuppage" android:exported="false" />
        <activity android:name="com.example.bustracker1.MainActivity" android:exported="false" />
        <activity android:name="com.example.bustracker1.PassengerBusSelectActivity" android:exported="false" />
        <activity android:name="com.example.bustracker1.drivermap" android:exported="false" android:label="@string/title_activity_drivermap" />
        <activity android:name="com.example.bustracker1.NotificationsListActivity" android:exported="false" />

    </application>
</manifest>
3. Clases de Modelo de Datos (POJOs)
Estas clases definen la estructura de la información que se guarda en la base de datos de Firebase. Son esenciales para mapear los datos JSON a objetos Java.

A. Modelo de Ubicación (latlon.java)
Se utiliza para guardar y recuperar las coordenadas GPS del autobús.

Ubicación: app/src/main/java/com/example/bustracker1/latlon.java

Java

package com.example.bustracker1;

public class latlon {
    // Variables privadas para proteger los datos
    private Double latitude;
    private Double longitude;

    // Constructor vacío: OBLIGATORIO para que Firebase funcione (serialización)
    public latlon() {
    }

    // Constructor completo: Para crear el objeto rápidamente con coordenadas
    public latlon(Double latitude, Double longitude){
        this.latitude = latitude;
        this.longitude = longitude;
    }

    // Getters y Setters para acceder y modificar los valores
    public Double getLatitude() { return latitude; }
    public void setLatitude(Double latitude) { this.latitude = latitude; }

    public Double getLongitude() { return longitude; }
    public void setLongitude(Double longitude) { this.longitude = longitude; }
}
B. Modelo de Notificación (NotificationItem.java)
Define la estructura de una alerta enviada por el conductor (ej. "Falla mecánica").

Ubicación: app/src/main/java/com/example/bustracker1/NotificationItem.java

Java

package com.example.bustracker1;

public class NotificationItem {
    private String title;   // Título de la alerta (ej. "Retraso")
    private String message; // Descripción detallada

    // Constructor vacío requerido por Firebase
    public NotificationItem() {
    }

    // Constructor para uso manual en la app
    public NotificationItem(String title, String message) {
        this.title = title;
        this.message = message;
    }

    // Métodos de acceso
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public String getMessage() { return message; }
    public void setMessage(String message) { this.message = message; }
}
C. Modelo de Pasajero (Pasajero.java)
Estructura los datos del perfil de un estudiante. Se usa en las listas de administración.

Ubicación: app/src/main/java/com/example/bustracker1/Pasajero.java

Java

package com.example.bustracker1;

public class Pasajero {
    String id; // El UID único de Firebase
    String nombre;
    String roll; // Matrícula o ID escolar
    String bus;  // Número de autobús asignado
    String apellidos; // Nombre del padre/tutor
    String telefono;

    public Pasajero() {} // Constructor vacío necesario para Firebase

    public Pasajero(String id, String nombre, String roll, String bus, String apellidos, String telefono) {
        this.id = id;
        this.nombre = nombre;
        this.roll = roll;
        this.bus = bus;
        this.apellidos = apellidos;
        this.telefono = telefono;
    }

    // Getters para leer los datos en las listas
    public String getId() { return id; }
    public String getNombre() { return nombre; }
    public String getRoll() { return roll; }
    public String getBus() { return bus; }
    public String getApellidos() { return apellidos; }
    public String getTelefono() { return telefono; }
}
D. Modelo de Registro de Estudiante (getdata.java)
Se utiliza específicamente durante el proceso de registro inicial (setuppage).

Ubicación: app/src/main/java/com/example/bustracker1/getdata.java

Java

package com.example.bustracker1;

public class getdata {
    // Datos personales del estudiante
    private String apellidosP;
    private String nompasajero;
    private String telefono;
    private String roll; // Matrícula
    private String busno;
    private String email;

    // Constructor vacío para Firebase
    public getdata(){
    }

    // Constructor para inicialización rápida
    public getdata(String apellidosP, String nompasajero, String telefono, String roll, String busno, String email) {
        this.apellidosP = apellidosP;
        this.nompasajero = nompasajero;
        this.telefono = telefono;
        this.roll = roll;
        this.busno = busno;
        this.email = email;
    }

    // Getters y Setters
    public String getBusno() { return busno; }
    public void setBusno(String busno) { this.busno = busno; }

    public String getApellidosP() { return apellidosP; }
    public void setApellidosP(String apellidosP) { this.apellidosP = apellidosP; }

    public String getNompasajero() { return nompasajero; }
    public void setNompasajero(String nompasajero) { this.nompasajero = nompasajero; }

    public String getTelefono() { return telefono; }
    public void setTelefono(String telefono) { this.telefono = telefono; }

    public String getRoll() { return roll; }
    public void setRoll(String roll) { this.roll = roll; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
E. Modelo de Registro de Conductor (getbusdriverdata.java)
Define los datos necesarios para registrar un nuevo conductor en el sistema.

Ubicación: app/src/main/java/com/example/bustracker1/getbusdriverdata.java

Java

package com.example.bustracker1;

public class getbusdriverdata {
    private String name;
    private String email;
    private String password;
    private String message; // Mensaje de estado opcional

    public getbusdriverdata(){
    }

    public getbusdriverdata(String name, String email, String password, String message){
        this.name = name;
        this.email = email;
        this.password = password;
        this.message = message;
    }

    // Métodos de acceso estándar
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }

    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }

    public String getMessage() { return message; }
    public void setMessage(String message) { this.message = message; }
}
