code (extended version)
#include <WiFi.h>
#include "esp_wifi.h"   // extra include → gives low-level ESP-IDF functions


imported esp_wifi.h → this is ESP-IDF’s Wi-Fi API, not part of the usual Arduino library.

This allows you to query detailed info about the access point (like security type, channel, BSSID, etc.).

WiFi.mode(WIFI_STA);  // put ESP32 into "station" mode (client mode, not access point)
WiFi.begin(ssid, password); // try to connect


👉 Standard connection sequence.

while (WiFi.status() != WL_CONNECTED) {
  delay(500);
  Serial.print(".");
}


 Same as the simple sketch: wait until connected.

wifi_ap_record_t apinfo;
if (esp_wifi_sta_get_ap_info(&apinfo) == ESP_OK) {


This is the new part.

wifi_ap_record_t is a struct that stores details about the Wi-Fi you’re connected to.

esp_wifi_sta_get_ap_info() fills that struct with data.

Serial.printf("SSID: %s\n", apinfo.ssid);
Serial.printf("Channel: %d\n", apinfo.primary);


👉 Shows which network SSID you connected to and which Wi-Fi channel (1–13 for 2.4 GHz).

switch (apinfo.authmode) {
  case WIFI_AUTH_WPA2_PSK: Serial.println("WPA2-PSK"); break;
  case WIFI_AUTH_WPA3_PSK: Serial.println("WPA3-SAE (Personal)"); break;
  case WIFI_AUTH_WPA2_WPA3_PSK: Serial.println("WPA2/WPA3 mixed"); break;
  ...
}


This is the special part: it prints what security type you’re actually using.

The basic Arduino WiFi library doesn’t tell you if you are on WPA2 or WPA3.

By calling esp_wifi_sta_get_ap_info(), you can see it directly from the ESP-IDF layer.

 So what’s the difference?

Simple sketch → just connects and shows IP.

Your sketch → does the same plus shows:

SSID

Wi-Fi channel

Auth mode (security type) = WPA2, WPA3, mixed, etc.

👉 code is more “professional” or diagnostic — useful for verifying real security connection (important since ESP32 sometimes silently falls back to WPA2).
