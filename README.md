# REGFORM
This is an assignment for Day 1
import React, { useState, useCallback } from 'react';
import { LogIn, Mail, Lock, CheckCircle, RotateCw } from 'lucide-react';

// --- STYLES AND CONSTANTS ---
const API_URL = "http://localhost:3000/api/register"; // Change this to your actual Node.js endpoint!

// Utility to simulate a secure hash on the client side for demonstration purposes only.
// The actual hashing MUST happen on the server (Node.js) using 'bcrypt'.
const mockHash = (password) => {
    const salt = Array.from({ length: 10 }, () => Math.random().toString(36)[2]).join('');
    // Mocking the format of a bcrypt hash for display
    return `$2b$10$${salt}$${btoa(password).substring(0, 30)}...`;
};

// --- COMPONENTS ---

/**
 * Renders the initial registration form view.
 */
const RegistrationForm = ({ onSubmit, isLoading }) => {
    const [formData, setFormData] = useState({ username: '', email: '', password: '' });
    const [error, setError] = useState('');

    const handleChange = useCallback((e) => {
        setFormData(prev => ({ ...prev, [e.target.name]: e.target.value }));
        setError('');
    }, []);

    const handleSubmit = (e) => {
        e.preventDefault();
        setError('');

        if (!formData.username || !formData.email || !formData.password) {
            setError('All fields are required.');
            return;
        }
        if (formData.password.length < 6) {
            setError('Password must be at least 6 characters long.');
            return;
        }

        onSubmit(formData);
    };

    return (
        <form onSubmit={handleSubmit} className="space-y-6">
            <h2 className="text-3xl font-extrabold text-gray-900 text-center">Create Your Account</h2>

            {/* Username Input */}
            <div>
                <label htmlFor="username" className="block text-sm font-medium text-gray-700 flex items-center mb-1">
                    <LogIn className="w-4 h-4 mr-2 text-indigo-500" /> Username
                </label>
                <input
                    id="username"
                    name="username"
                    type="text"
                    required
                    value={formData.username}
                    onChange={handleChange}
                    className="w-full px-4 py-3 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 transition duration-150"
                    placeholder="Choose a username"
                    disabled={isLoading}
                />
            </div>

            {/* Email Input */}
            <div>
                <label htmlFor="email" className="block text-sm font-medium text-gray-700 flex items-center mb-1">
                    <Mail className="w-4 h-4 mr-2 text-indigo-500" /> Email
                </label>
                <input
                    id="email"
                    name="email"
                    type="email"
                    required
                    value={formData.email}
                    onChange={handleChange}
                    className="w-full px-4 py-3 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 transition duration-150"
                    placeholder="you@example.com"
                    disabled={isLoading}
                />
            </div>

            {/* Password Input */}
            <div>
                <label htmlFor="password" className="block text-sm font-medium text-gray-700 flex items-center mb-1">
                    <Lock className="w-4 h-4 mr-2 text-indigo-500" /> Password
                </label>
                <input
                    id="password"
                    name="password"
                    type="password"
                    required
                    value={formData.password}
                    onChange={handleChange}
                    className="w-full px-4 py-3 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 transition duration-150"
                    placeholder="Minimum 6 characters"
                    disabled={isLoading}
                />
            </div>

            {/* Error Message */}
            {error && (
                <div className="p-3 text-sm font-medium text-red-700 bg-red-100 rounded-lg border border-red-300">
                    {error}
                </div>
            )}

            {/* Submit Button */}
            <button
                type="submit"
                disabled={isLoading}
                className="w-full flex justify-center items-center py-3 px-4 border border-transparent rounded-lg shadow-md text-base font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-150 disabled:opacity-50 disabled:cursor-not-allowed"
            >
                {isLoading ? (
                    <>
                        <RotateCw className="w-5 h-5 mr-2 animate-spin" />
                        Registering...
                    </>
                ) : (
                    'Register'
                )}
            </button>
        </form>
    );
};

/**
 * Renders the success view after registration.
 */
const WelcomeScreen = ({ username, hashedPassword, onReset }) => (
    <div className="text-center p-8 bg-white rounded-xl shadow-2xl space-y-8">
        <CheckCircle className="w-16 h-16 mx-auto text-green-500 animate-bounce" />
        <h1 className="text-4xl font-extrabold text-gray-900">Welcome, {username}!</h1>
        <p className="text-xl text-gray-600">Your account has been successfully registered.</p>
        <div className="bg-gray-50 p-6 rounded-lg border border-gray-200">
            <h3 className="text-lg font-semibold text-gray-700 mb-2">Securely Stored Password Hash:</h3>
            <code className="block text-xs md:text-sm text-indigo-800 break-all p-3 bg-gray-100 rounded-md select-all">
                {hashedPassword}
            </code>
            <p className="mt-3 text-xs text-gray-500">
                This is the secure, one-way hash stored in the database. The original password cannot be recovered from this value.
            </p>
        </div>
        <button
            onClick={onReset}
            className="mt-6 w-full py-3 px-4 border border-transparent rounded-lg shadow-md text-base font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-150"
        >
            Go Back to Form
        </button>
    </div>
);


/**
 * Main application component managing state and views.
 */
const App = () => {
    const [step, setStep] = useState('form'); // 'form' or 'welcome'
    const [isLoading, setIsLoading] = useState(false);
    const [registeredUser, setRegisteredUser] = useState(null);

    // This function simulates the API call to your Node.js backend
    const handleRegistration = async (formData) => {
        setIsLoading(true);
        console.log("Attempting to register user with data:", formData);

        try {
            // --- SIMULATION OF BACKEND PROCESS ---
            await new Promise(r => setTimeout(r, 1500)); // Simulate network latency
            const hashedPassword = mockHash(formData.password);

            // Assuming registration was successful, save user details and move to welcome screen
            setRegisteredUser({
                username: formData.username,
                hashedPassword: hashedPassword
            });
            setStep('welcome');

            // --- ACTUAL API CALL (Uncomment and replace with your actual endpoint) ---
            /*
            const response = await fetch(API_URL, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(formData)
            });

            const result = await response.json();

            if (response.ok) {
                setRegisteredUser({
                    username: formData.username,
                    // In a real app, the server might return the hash for display, or just success.
                    // For this example, we mock it.
                    hashedPassword: mockHash(formData.password) 
                });
                setStep('welcome');
            } else {
                console.error("Registration failed:", result.message);
                // Handle server-side errors (e.g., email already exists)
            }
            */

        } catch (error) {
            console.error('Network or mock error during registration:', error);
        } finally {
            setIsLoading(false);
        }
    };

    const resetApp = () => {
        setStep('form');
        setRegisteredUser(null);
    };

    return (
        <div className="min-h-screen bg-gray-100 flex items-center justify-center p-4">
            <div className="w-full max-w-md">
                <div className="bg-white p-8 md:p-10 rounded-xl shadow-2xl transform transition duration-500 hover:shadow-3xl">
                    {step === 'form' && (
                        <RegistrationForm onSubmit={handleRegistration} isLoading={isLoading} />
                    )}
                    {step === 'welcome' && registeredUser && (
                        <WelcomeScreen
                            username={registeredUser.username}
                            hashedPassword={registeredUser.hashedPassword}
                            onReset={resetApp}
                        />
                    )}
                </div>
            </div>
        </div>
    );
};

export default App;<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Frontend File Open and Save</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    <style>
        /* Custom scrollbar for the textarea */
        textarea::-webkit-scrollbar {
            width: 8px;
        }
        textarea::-webkit-scrollbar-track {
            background: #f7f7f7;
        }
        textarea::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
    </style>
</head>
<body class="bg-gray-50 flex items-center justify-center min-h-screen p-4 font-sans">

    <div class="w-full max-w-2xl bg-white shadow-xl rounded-xl p-6 space-y-6">
        <h1 class="text-3xl font-bold text-gray-800 border-b pb-2 mb-4">Frontend File Utility</h1>
        <p class="text-sm text-gray-600">This demonstrates file opening (reading) and saving (downloading) purely within the browser.</p>

        <!-- Container for File Operations -->
        <div class="space-y-4">
            
            <!-- 1. Open File Section -->
            <div class="bg-blue-50 p-4 rounded-lg border border-blue-200">
                <h2 class="text-xl font-semibold text-blue-800 mb-3">1. Open a Local File (Read)</h2>
                <input type="file" id="fileInput" accept=".txt, .html, .js, .json" class="hidden" />
                
                <button id="openFileBtn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded-lg transition duration-150 shadow-md hover:shadow-lg">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-2" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L9 11.586V3a1 1 0 112 0v8.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                    Select File to Load
                </button>
            </div>

            <!-- Content Area -->
            <textarea id="fileContent" rows="10" placeholder="File content will appear here, or you can type new data..." 
                      class="w-full p-4 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500 text-sm resize-none"></textarea>

            <!-- 2. Save File Section -->
            <div class="bg-green-50 p-4 rounded-lg border border-green-200">
                <h2 class="text-xl font-semibold text-green-800 mb-3">2. Save File (Download)</h2>
                <button id="saveFileBtn" class="w-full bg-green-600 hover:bg-green-700 text-white font-semibold py-2 px-4 rounded-lg transition duration-150 shadow-md hover:shadow-lg">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-2" viewBox="0 0 20 20" fill="currentColor">
                        <path d="M10 2a1 1 0 00-1 1v7.586l-1.293-1.293a1 1 0 00-1.414 1.414l3 3a1 1 0 001.414 0l3-3a1 1 0 00-1.414-1.414L11 10.586V3a1 1 0 00-1-1z" />
                        <path d="M5 16h10a2 2 0 002-2v-4a1 1 0 10-2 0v4a.5.5 0 01-.5.5h-9a.5.5 0 01-.5-.5v-4a1 1 0 10-2 0v4a2 2 0 002 2z" />
                    </svg>
                    Download Content as 'my_data.txt'
                </button>
            </div>
            
            <!-- Status Message Area -->
            <div id="statusMessage" class="text-center p-2 rounded-lg text-sm transition duration-300 opacity-0 h-8"></div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const fileInput = document.getElementById('fileInput');
            const openFileBtn = document.getElementById('openFileBtn');
            const saveFileBtn = document.getElementById('saveFileBtn');
            const fileContentArea = document.getElementById('fileContent');
            const statusMessage = document.getElementById('statusMessage');

            /**
             * Utility function to display a temporary status message.
             * @param {string} message - The message to display.
             * @param {string} type - 'success' or 'error' for styling.
             */
            function showStatus(message, type) {
                statusMessage.textContent = message;
                statusMessage.className = 'text-center p-2 rounded-lg text-sm transition duration-300 h-8'; // Reset classes
                statusMessage.classList.add('opacity-100', 'h-auto');
                
                if (type === 'success') {
                    statusMessage.classList.add('bg-green-100', 'text-green-700');
                } else if (type === 'error') {
                    statusMessage.classList.add('bg-red-100', 'text-red-700');
                } else {
                    statusMessage.classList.add('bg-gray-100', 'text-gray-700');
                }

                // Hide the message after 3 seconds
                setTimeout(() => {
                    statusMessage.classList.remove('opacity-100', 'h-auto');
                    statusMessage.classList.add('opacity-0', 'h-8');
                }, 3000);
            }

            // --- 1. OPEN (READ) FILE FUNCTIONALITY ---

            // Trigger the hidden file input when the button is clicked
            openFileBtn.addEventListener('click', () => {
                fileInput.click();
            });

            // Handle file selection
            fileInput.addEventListener('change', (event) => {
                const file = event.target.files[0];

                if (!file) {
                    showStatus('No file selected.', 'error');
                    return;
                }

                // Use the FileReader API to read the content of the file
                const reader = new FileReader();

                // What to do when the file is successfully loaded
                reader.onload = (e) => {
                    try {
                        fileContentArea.value = e.target.result;
                        showStatus(`File loaded successfully: ${file.name}`, 'success');
                    } catch (err) {
                        console.error('Error parsing file content:', err);
                        showStatus('Error reading file content.', 'error');
                    }
                };

                // What to do if an error occurs during reading
                reader.onerror = () => {
                    console.error('FileReader error:', reader.error);
                    showStatus('Could not read the file.', 'error');
                };

                // Start reading the file as plain text
                reader.readAsText(file);
                
                // Clear the input value so the same file can be loaded again
                fileInput.value = ''; 
            });


            // --- 2. SAVE (DOWNLOAD) FILE FUNCTIONALITY ---

            saveFileBtn.addEventListener('click', () => {
                const contentToSave = fileContentArea.value;
                const filename = 'my_data.txt';
                
                if (contentToSave.trim() === '') {
                    showStatus('Cannot save empty content.', 'error');
                    return;
                }

                // Create a Blob (Binary Large Object) from the text content
                const blob = new Blob([contentToSave], { type: 'text/plain;charset=utf-8' });

                // Create a temporary URL for the Blob
                const url = URL.createObjectURL(blob);

                // Create a temporary <a> element to trigger the download
                const a = document.createElement('a');
                a.style.display = 'none';
                a.href = url;
                a.download = filename; // Set the desired default filename

                // Append the link to the document, trigger the click, and remove it
                document.body.appendChild(a);
                a.click();
                
                // Clean up the temporary URL and element
                document.body.removeChild(a);
                URL.revokeObjectURL(url);

                showStatus(`Content downloaded as: ${filename}`, 'success');
            });

        });
    </script>
</body>
</html>
SERVER.JS
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();

const authRoutes = require('./routes/auth');

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Connect to MongoDB
mongoose.connect(process.env.MONGODB_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true
})
.then(() => console.log('✅ Connected to MongoDB'))
.catch((err) => console.error('❌ MongoDB connection error:', err));

// Routes
app.use('/api/auth', authRoutes);

// Health check endpoint
app.get('/', (req, res) => {
  res.json({ message: 'Kaar Technologies API is running' });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`🚀 Server running on port ${PORT}`);
});
models/User.js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    trim: true
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    trim: true
  },
  password: {
    type: String,
    required: true
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

module.exports = mongoose.model('User', userSchema);
package.json
{
  "name": "kaar-backend",
  "version": "1.0.0",
  "description": "Kaar Technologies Registration Backend",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^7.0.0",
    "bcryptjs": "^2.4.3",
    "dotenv": "^16.0.3",
    "cors": "^2.8.5"
  },
  "devDependencies": {
    "nodemon": "^2.0.22"
  }
}
