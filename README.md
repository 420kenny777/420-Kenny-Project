// LoginPage.jsx
import React, { useState } from "react";

const registeredUsersKey = "420kennyRegisteredUsers";

export default function LoginPage({ onLogin }) {
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");
  const [isRegistering, setIsRegistering] = useState(false);
  const [email, setEmail] = useState("");
  const [verificationCode, setVerificationCode] = useState("");
  const [codeSent, setCodeSent] = useState(false);
  const [enteredCode, setEnteredCode] = useState("");

  // Hardcoded admin user:
  const adminUser = {
    username: "420kennymadeit",
    password: "420mademe",
    email: "admin@420kenny.com",
    verified: true,
  };

  const getRegisteredUsers = () => {
    const users = localStorage.getItem(registeredUsersKey);
    return users ? JSON.parse(users) : [];
  };

  const saveRegisteredUsers = (users) => {
    localStorage.setItem(registeredUsersKey, JSON.stringify(users));
  };

  const sendVerificationCode = (email) => {
    // Simulate sending a 6-digit code (in real app you'd email this)
    const code = Math.floor(100000 + Math.random() * 900000).toString();
    setVerificationCode(code);
    alert(`Verification code sent to ${email}: ${code}`);
    setCodeSent(true);
  };

  const handleRegister = () => {
    if (!username || !password || !email) {
      setMessage("Please fill in all fields.");
      return;
    }
    const users = getRegisteredUsers();
    if (users.find((u) => u.username === username)) {
      setMessage("Username already exists.");
      return;
    }
    sendVerificationCode(email);
  };

  const handleVerify = () => {
    if (enteredCode === verificationCode) {
      const users = getRegisteredUsers();
      users.push({ username, password, email, verified: true });
      saveRegisteredUsers(users);
      setMessage("Registration successful! Please log in.");
      setIsRegistering(false);
      setCodeSent(false);
      setVerificationCode("");
      setUsername("");
      setPassword("");
      setEmail("");
      setEnteredCode("");
    } else {
      setMessage("Incorrect verification code.");
    }
  };

  const handleLogin = () => {
    if (username === adminUser.username && password === adminUser.password) {
      onLogin(adminUser);
      return;
    }
    const users = getRegisteredUsers();
    const user = users.find(
      (u) => u.username === username && u.password === password && u.verified
    );
    if (user) {
      onLogin(user);
    } else {
      setMessage("Invalid credentials or email not verified.");
    }
  };

  return (
    <div className="max-w-md mx-auto mt-20 bg-gray-900 p-8 rounded-xl">
      <h1 className="text-3xl mb-6 text-center font-bold">420 Kenny Login</h1>

      {!isRegistering && (
        <>
          <input
            type="text"
            placeholder="Username"
            className="w-full p-2 mb-4 bg-gray-800 text-white rounded"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
          />
          <input
            type="password"
            placeholder="Password"
            className="w-full p-2 mb-4 bg-gray-800 text-white rounded"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
          <button
            className="w-full bg-gold text-black py-2 rounded mb-4 font-bold"
            onClick={handleLogin}
          >
            Log In
          </button>
          <p className="text-center">
            Don't have an account?{" "}
            <button
              className="text-gold underline"
              onClick={() => {
                setIsRegistering(true);
                setMessage("");
              }}
            >
              Register
            </button>
          </p>
        </>
      )}

      {isRegistering && !codeSent && (
        <>
          <input
            type="text"
            placeholder="Username"
            className="w-full p-2 mb-4 bg-gray-800 text-white rounded"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
          />
          <input
            type="email"
            placeholder="Email"
            className="w-full p-2 mb-4 bg-gray-800 text-white rounded"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <input
            type="password"
            placeholder="Password"
            className="w-full p-2 mb-4 bg-gray-800 text-white rounded"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
          <button
            className="w-full bg-gold text-black py-2 rounded mb-4 font-bold"
            onClick={handleRegister}
          >
            Send Verification Code
          </button>
          <button
            className="w-full bg-gray-700 text-white py-2 rounded"
            onClick={() => {
              setIsRegistering(false);
              setMessage("");
            }}
          >
            Back to Login
          </button>
        </>
      )}

      {isRegistering && codeSent && (
        <>
          <p className="mb-4">
            Enter the verification code sent to <strong>{email}</strong>
          </p>
          <input
            type="text"
            placeholder="Verification Code"
            className="w-full p-2 mb-4 bg-gray-800 text-white rounded"
            value={enteredCode}
            onChange={(e) => setEnteredCode(e.target.value)}
          />
          <button
            className="w-full bg-gold text-black py-2 rounded mb-4 font-bold"
            onClick={handleVerify}
          >
            Verify & Register
          </button>
          <button
            className="w-full bg-gray-700 text-white py-2 rounded"
            onClick={() => {
              setIsRegistering(false);
              setCodeSent(false);
              setVerificationCode("");
              setMessage("");
            }}
          >
            Cancel
          </button>
        </>
      )}

      {message && <p className="mt-4 text-center text-red-500">{message}</p>}
    </div>
  );
}
