I cannot generate a downloadable PDF file directly. However, I can provide you with the exact content, code, and structure you need.

Instructions to create your PDF:

Copy the content below into Microsoft Word or Google Docs.

Take screenshots of your running emulator/device.

Paste the screenshots in the "Output Screenshots" section.

Upload your code to GitHub and paste the link in the "GitHub Link" section.

Save the document as a PDF.

Project Report: Flutter Firebase Authentication (Login & Signup)
1. Project Overview
This project demonstrates the implementation of a user authentication system using Flutter for the frontend and Firebase Authentication for the backend. The app supports:

User Registration (Sign Up)

User Login (Sign In)

Logout Functionality

Navigation between screens based on authentication state.

2. Dependencies (pubspec.yaml)
The following packages were added to connect the app with Firebase:

YAML

dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2  # Core Firebase package
  firebase_auth: ^4.16.0  # Authentication package
3. Source Code
A. Auth Service (auth_service.dart) Handles the logic for communicating with Firebase.

Dart

import 'package:firebase_auth/firebase_auth.dart';

class AuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;

  // Sign Up
  Future<User?> signUp(String email, String password) async {
    try {
      UserCredential result = await _auth.createUserWithEmailAndPassword(
        email: email, 
        password: password
      );
      return result.user;
    } catch (e) {
      print(e.toString());
      return null;
    }
  }

  // Sign In
  Future<User?> signIn(String email, String password) async {
    try {
      UserCredential result = await _auth.signInWithEmailAndPassword(
        email: email, 
        password: password
      );
      return result.user;
    } catch (e) {
      print(e.toString());
      return null;
    }
  }

  // Sign Out
  Future<void> signOut() async {
    await _auth.signOut();
  }
}
B. Login Screen (login_screen.dart) Allows users to input credentials to access the app.

Dart

import 'package:flutter/material.dart';
import 'auth_service.dart';
import 'signup_screen.dart';
import 'home_screen.dart';

class LoginScreen extends StatefulWidget {
  @override
  _LoginScreenState createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  final AuthService _auth = AuthService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Login")),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _emailController,
              decoration: InputDecoration(labelText: "Email"),
            ),
            TextField(
              controller: _passwordController,
              decoration: InputDecoration(labelText: "Password"),
              obscureText: true,
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () async {
                var user = await _auth.signIn(
                  _emailController.text, 
                  _passwordController.text
                );
                if (user != null) {
                  Navigator.pushReplacement(context, 
                    MaterialPageRoute(builder: (_) => HomeScreen()));
                } else {
                  ScaffoldMessenger.of(context).showSnackBar(
                    SnackBar(content: Text("Login Failed"))
                  );
                }
              },
              child: Text("Login"),
            ),
            TextButton(
              onPressed: () {
                Navigator.push(context, 
                  MaterialPageRoute(builder: (_) => SignupScreen()));
              },
              child: Text("Don't have an account? Sign Up"),
            )
          ],
        ),
      ),
    );
  }
}
C. Signup Screen (signup_screen.dart) Allows new users to register an account.

Dart

import 'package:flutter/material.dart';
import 'auth_service.dart';

class SignupScreen extends StatefulWidget {
  @override
  _SignupScreenState createState() => _SignupScreenState();
}

class _SignupScreenState extends State<SignupScreen> {
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  final AuthService _auth = AuthService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Sign Up")),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _emailController,
              decoration: InputDecoration(labelText: "Email"),
            ),
            TextField(
              controller: _passwordController,
              decoration: InputDecoration(labelText: "Password"),
              obscureText: true,
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () async {
                var user = await _auth.signUp(
                  _emailController.text, 
                  _passwordController.text
                );
                if (user != null) {
                  Navigator.pop(context); // Go back to login
                  ScaffoldMessenger.of(context).showSnackBar(
                    SnackBar(content: Text("Account Created! Please Login."))
                  );
                }
              },
              child: Text("Sign Up"),
            ),
          ],
        ),
      ),
    );
  }
}
D. Main Entry Point (main.dart) Initializes Firebase.

Dart

import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'login_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: LoginScreen(),
    );
  }
}
