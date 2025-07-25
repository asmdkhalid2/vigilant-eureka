# Very-assistant-
[7/5, 7:04 PM] Khalid: import 'package:flutter/material.dart';
import 'package:google_maps_flutter/google_maps_flutter.dart';
import 'package:location/location.dart';
dependencies:
  flutter:
    sdk: flutter
  google_maps_flutter: ^2.6.1
  location: ^5.0.3
  flutter_tts: ^3.5.0
  speech_to_text: ^5.6.0
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.locationapp">

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:label="locationapp"
        android:icon="@mipmap/ic_launcher">

        <!-- Google Maps API Key -->
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="AIzaSyXXXXXX_YOUR_KEY_HERE" />
import 'package:flutter/material.dart';
import 'package:google_maps_flutter/google_maps_flutter.dart';
import 'package:location/location.dart';
import 'package:flutter_tts/flutter_tts.dart';
import 'package:speech_to_text/speech_to_text.dart' as stt;

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Location + Voice Demo',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: const LocationTrackingPage(),
    );
  }
}

class LocationTrackingPage extends StatefulWidget {
  const LocationTrackingPage({super.key});
  @override
  State<LocationTrackingPage> createState() => _LocationTrackingPageState();
}

class _LocationTrackingPageState extends State<LocationTrackingPage> {
  Location location = Location();
  GoogleMapController? mapController;
  LatLng? currentPosition;

  late FlutterTts flutterTts;
  late stt.SpeechToText speech;
  bool isListening = false;
  String lastWords = "";

  @override
  void initState() {
    super.initState();
    flutterTts = FlutterTts();
    speech = stt.SpeechToText();

    requestLocationPermission();

    // Initialize speech recognition
    initSpeech();
  }

  void requestLocationPermission() async {
    bool serviceEnabled = await location.serviceEnabled();
    if (!serviceEnabled) {
      serviceEnabled = await location.requestService();
      if (!serviceEnabled) return;
    }

    PermissionStatus permissionGranted = await location.hasPermission();
    if (permissionGranted == PermissionStatus.denied) {
      permissionGranted = await location.requestPermission();
      if (permissionGranted != PermissionStatus.granted) return;
    }

    // Start listening location changes
    location.onLocationChanged.listen((loc) {
      setState(() {
        currentPosition = LatLng(loc.latitude!, loc.longitude!);
      });
      mapController?.animateCamera(CameraUpdate.newLatLng(currentPosition!));

      flutterTts.speak(
          "Location updated to latitude ${loc.latitude!.toStringAsFixed(4)}, longitude ${loc.longitude!.toStringAsFixed(4)}");
    });
  }

  void initSpeech() async {
    bool available = await speech.initialize(
      onStatus: (val) {
        if (val == "done") {
          setState(() => isListening = false);
          processVoiceCommand(lastWords);
        }
      },
      onError: (val) => print('Speech error: $val'),
    );

    if (!available) {
      print("Speech recognition not available");
    }
  }

  void startListening() async {
    if (!isListening) {
      setState(() {
        isListening = true;
        lastWords = "";
      });

      await speech.listen(
        onResult: (result) {
          setState(() {
            lastWords = result.recognizedWords;
          });
        },
        listenFor: const Duration(seconds: 5),
        localeId: 'en_US',
        cancelOnError: true,
        partialResults: false,
      );
    }
  }

  void processVoiceCommand(String command) {
    print("Recognized command: $command");
    if (command.toLowerCase().contains("locate me") ||
        command.toLowerCase().contains("my location")) {
      flutterTts.speak("Locating you now.");
      location.getLocation().then((loc) {
        setState(() {
          currentPosition = LatLng(loc.latitude!, loc.longitude!);
        });
        mapController?.animateCamera(CameraUpdate.newLatLng(currentPosition!));
      });
    } else {
      flutterTts.speak("Sorry, I did not understand the command.");
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Location + Voice Demo'),
        actions: [
          IconButton(
            icon: Icon(isListening ? Icons.mic : Icons.mic_none),
            onPressed: startListening,
          )
        ],
      ),
      body: currentPosition == null
          ? const Center(child: CircularProgressIndicator())
          : GoogleMap(
              initialCameraPosition:
                  CameraPosition(target: currentPosition!, zoom: 15),
              myLocationEnabled: true,
              onMapCreated: (controller) => mapController = controller,
            ),
    );
  }
}
    </application>
</manifest>
class LocationTrackingPage extends StatefulWidget {
  @override
  _LocationTrackingPageState createState() => _LocationTrackingPageState();
}

class _LocationTrackingPageState extends State<LocationTrackingPage> {
  Location location = Location();
  GoogleMapController? mapController;
  LatLng? currentPosition;

  @override
  void initState() {
    super.initState();
    location.requestPermission().then((granted) {
      if (granted == PermissionStatus.granted) {
        location.onLocationChanged.listen((loc) {
          setState(() {
            currentPosition = LatLng(loc.latitude!, loc.longitude!);
          });
          mapController?.animateCamera(CameraUpdate.newLatLng(currentPosition!));
        });
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Location Tracking')),
      body: currentPosition == null
          ? Center(child: CircularProgressIndicator())
          : GoogleMap(
              initialCameraPosition: CameraPosition(
                target: currentPosition!,
                zoom: 15,
              ),
              myLocationEnabled: true,
              onMapCreated: (controller) => mapController = controller,
            ),
    );
  }
}
[7/6, 1:24 AM] Khalid: dependencies:
  flutter:
    sdk: flutter
  google_maps_flutter: ^2.5.0
  location: ^5.0.3
[7/6, 1:25 AM] Khalid: <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
[7/6, 2:03 AM] Khalid: // লোকেশন আপডেট শুনে ম্যাপ ক্যামেরা অ্যানিমেট
location.onLocationChanged.listen((loc) {
  setState(() {
    currentPosition = LatLng(loc.latitude!, loc.longitude!);
  });
  mapController?.animateCamera(CameraUpdate.newLatLng(currentPosition!));
});
[7/6, 2:10 AM] Khalid: dependencies:
  flutter:
    sdk: flutter
  google_maps_flutter: ^2.6.1
  location: ^4.4.0
  flutter_tts: ^3.5.0
  speech_to_text: ^5.6.0
[7/6, 2:10 AM] Khalid: <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />

<application>
    <!-- Google Maps API Key এখানে বসাও -->
    <meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="AIzaSyXXXXXX_YOUR_KEY_HERE" />
</application>
