# Very-assistant-
[7/5, 7:04 PM] Khalid: import 'package:flutter/material.dart';
import 'package:google_maps_flutter/google_maps_flutter.dart';
import 'package:location/location.dart';

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