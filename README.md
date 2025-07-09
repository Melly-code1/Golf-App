git init
npx create-expo-app golf-scorekeeper
cd golf-scorekeeper
npm install firebase react-native-paper react-navigation react-native-image-picker
// firebaseConfig.js
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAuth } from "firebase/auth";
import { getStorage } from "firebase/storage";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
export const auth = getAuth(app);
export const storage = getStorage(app);

// AuthScreen.js
import React, { useState } from 'react';
import { View, TextInput, Button, Text } from 'react-native';
import { auth } from './firebaseConfig';
import { signInWithEmailAndPassword, createUserWithEmailAndPassword } from 'firebase/auth';

export default function AuthScreen() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = () => signInWithEmailAndPassword(auth, email, password);
  const handleSignup = () => createUserWithEmailAndPassword(auth, email, password);

  return (
    <View>
      <TextInput placeholder="Email" onChangeText={setEmail} />
      <TextInput placeholder="Password" secureTextEntry onChangeText={setPassword} />
      <Button title="Login" onPress={handleLogin} />
      <Button title="Sign Up" onPress={handleSignup} />
    </View>
  );
}

// AddGameScreen.js
import React, { useState } from 'react';
import { View, TextInput, Button } from 'react-native';
import { db, auth } from './firebaseConfig';
import { collection, addDoc, Timestamp } from 'firebase/firestore';

export default function AddGameScreen() {
  const [course, setCourse] = useState('');
  const [score, setScore] = useState('');

  const handleAddGame = async () => {
    await addDoc(collection(db, 'games'), {
      userId: auth.currentUser.uid,
      course,
      score: parseInt(score),
      createdAt: Timestamp.now()
    });
  };

  return (
    <View>
      <TextInput placeholder="Course Name" onChangeText={setCourse} />
      <TextInput placeholder="Score" keyboardType="numeric" onChangeText={setScore} />
      <Button title="Save Game" onPress={handleAddGame} />
    </View>
  );
}
