import 'package:flutter/material.dart';
import 'package:speech_to_text/speech_to_text.dart';
import 'package:flutter_tts/flutter_tts.dart';
import 'package:url_launcher/url_launcher.dart';

void main() {
  runApp(NoriApp());
}

class NoriApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: NoriHome(),
    );
  }
}

class NoriHome extends StatefulWidget {
  @override
  State<NoriHome> createState() => _NoriHomeState();
}

class _NoriHomeState extends State<NoriHome> {
  SpeechToText stt = SpeechToText();
  FlutterTts tts = FlutterTts();

  String text = "Nori hazır 🌿🤖";
  bool isListening = false;

  @override
  void initState() {
    super.initState();
    tts.setLanguage("tr-TR");
  }

  Future<void> speak(String msg) async {
    await tts.speak(msg);
  }

  Future<void> listen() async {
    if (!isListening) {
      bool available = await stt.initialize();
      if (!available) return;

      setState(() => isListening = true);

      stt.listen(onResult: (result) {
        String command = result.recognizedWords;
        setState(() {
          text = command;
        });

        if (result.finalResult) {
          isListening = false;
          handleCommand(command);
        }
      });
    } else {
      setState(() => isListening = false);
      stt.stop();
    }
  }

  void handleCommand(String cmd) {
    cmd = cmd.toLowerCase();

    // 🔔 ALARM (simülasyon)
    if (cmd.contains("alarm")) {
      speak("Alarm kuruldu");
      setState(() => text = "⏰ Alarm ayarlandı (simülasyon)");
      return;
    }

    // 📞 ARAMA
    if (cmd.contains("ara")) {
      makeCall(cmd);
      return;
    }

    // ➗ HESAP
    if (cmd.contains("hesapla")) {
      calculate(cmd);
      return;
    }

    // ❓ NE DEMEK
    if (cmd.contains("ne demek")) {
      speak("Bunu açıklıyorum");
      setState(() => text = "AI açıklama modu aktif 🤖");
      return;
    }

    // 💬 SESLİ MESAJ (simülasyon)
    if (cmd.contains("sesli mesaj")) {
      speak("Sesli mesaj özelliği açıldı");
      return;
    }

    // 🧠 GENEL AI
    if (cmd.startsWith("nori")) {
      speak("Bunu anlıyorum ve işliyorum");
      setState(() => text = "AI komut işlendi 🤖");
      return;
    }

    speak("Komutu anlayamadım");
  }

  void makeCall(String cmd) async {
    speak("Arama başlatılıyor");

    // demo numara
    final Uri uri = Uri.parse("tel:123456789");
    await launchUrl(uri);
  }

  void calculate(String cmd) {
    try {
      // çok basit demo hesap
      cmd = cmd.replaceAll("nori", "");
      cmd = cmd.replaceAll("hesapla", "");

      String expression = cmd.trim();

      // ⚠️ basit eval (demo)
      double result = _eval(expression);

      speak("Sonuç $result");
      setState(() => text = "Sonuç: $result");
    } catch (e) {
      speak("Hesaplanamadı");
    }
  }

  double _eval(String expr) {
    // SADE DEMO: sadece + destekler
    List<String> parts = expr.split("+");
    double sum = 0;

    for (var p in parts) {
      sum += double.tryParse(p.trim()) ?? 0;
    }

    return sum;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Nori 🌿🤖"),
        backgroundColor: Colors.green,
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              text,
              textAlign: TextAlign.center,
              style: TextStyle(fontSize: 18),
            ),
            SizedBox(height: 30),

            ElevatedButton(
              onPressed: listen,
              child: Text(isListening ? "Dinliyor..." : "Konuş 🎤"),
            ),
          ],
        ),
      ),
    );
  }
}# -Nori
AI sesli asistan
