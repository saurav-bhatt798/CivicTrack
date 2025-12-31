# CivicTrack - Community Issue Reporting System

CivicTrack is a production-ready React + Firebase application that enables citizens to report public issues (road damage, water leaks, streetlight issues, etc.) and allows authorities to manage and track these reports efficiently.

## Features

### For Citizens
- ✅ Sign up/Login (Email + Google OAuth)
- ✅ Create reports with GPS location, photos, and categories
- ✅ View all issues on an interactive map
- ✅ Track report status (pending → in-progress → resolved)
- ✅ Comment on reports
- ✅ Upvote issues
- ✅ Receive notifications when status changes
- ✅ Verify resolution

### For Administrators
- ✅ Secure admin dashboard
- ✅ View all reports in table and map views
- ✅ Filter by department, status, and date
- ✅ Assign departments to reports
- ✅ Change report status
- ✅ Add admin notes
- ✅ Analytics dashboard with:
  - Unresolved count
  - Average resolution time
  - Reports by category
  - Status distribution
- ✅ Export reports to CSV

### Technical Features
- ✅ Real-time updates using Firestore
- ✅ Offline support (PWA)
- ✅ Push notifications (FCM)
- ✅ Image upload to Firebase Storage
- ✅ Secure Firestore rules
- ✅ Cloud Functions for automated workflows
- ✅ Responsive design (mobile + desktop)
- ✅ Dark/Light mode support

## Tech Stack

- **Frontend**: React 18 + Vite
- **UI Libraries**: Material UI + Tailwind CSS
- **Maps**: Leaflet + React-Leaflet
- **Backend**: Firebase
  - Authentication
  - Firestore (Database)
  - Storage (Images)
  - Cloud Functions
  - FCM (Notifications)
  - Hosting
- **Animations**: Framer Motion
- **Charts**: Recharts
- **PWA**: Vite PWA Plugin

## Getting Started

### Prerequisites

- Node.js 18+ and npm
- Firebase account and project
- Git

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd firebase-auth-app
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up Firebase**
   - Go to [Firebase Console](https://console.firebase.google.com/)
   - Create a new project
   - Enable Authentication (Email/Password and Google)
   - Enable Firestore Database
   - Enable Storage
   - Enable Cloud Functions
   - Enable Cloud Messaging (FCM)

4. **Configure environment variables**
   
   Create a `.env` file in the root directory:
   ```env
   VITE_FIREBASE_API_KEY=your_api_key
   VITE_FIREBASE_AUTH_DOMAIN=your_project_id.firebaseapp.com
   VITE_FIREBASE_PROJECT_ID=your_project_id
   VITE_FIREBASE_STORAGE_BUCKET=your_project_id.appspot.com
   VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
   VITE_FIREBASE_APP_ID=your_app_id
   VITE_FIREBASE_MEASUREMENT_ID=your_measurement_id
   VITE_FIREBASE_VAPID_KEY=your_vapid_key
   ```

   You can find these values in Firebase Console → Project Settings → General → Your apps

   For VAPID key:
   - Go to Firebase Console → Project Settings → Cloud Messaging
   - Generate a new key pair under "Web Push certificates"
   - Use the key pair string as `VITE_FIREBASE_VAPID_KEY`

5. **Install Firebase CLI** (if not already installed)
   ```bash
   npm install -g firebase-tools
   ```

6. **Login to Firebase**
   ```bash
   firebase login
   ```

7. **Initialize Firebase project**
   ```bash
   firebase init
   ```
   
   Select:
   - Firestore: Yes (use existing rules file)
   - Functions: Yes
   - Hosting: Yes (use dist as public directory)
   - Storage: Yes (use existing rules file)

8. **Deploy Firestore rules and indexes**
   ```bash
   firebase deploy --only firestore:rules,firestore:indexes
   ```

9. **Deploy Storage rules**
   ```bash
   firebase deploy --only storage
   ```

10. **Set up Cloud Functions**
    ```bash
    cd functions
    npm install
    cd ..
    ```

    Deploy functions:
    ```bash
    firebase deploy --only functions
    ```

11. **Create admin user**
    
    Option 1: Using Firebase Console
    - Go to Authentication → Users
    - Add a user manually
    - Go to Firestore → users collection
    - Create a document with the user's UID
    - Set the `role` field to `"admin"`

    Option 2: Using seed script (recommended for demo)
    ```bash
    # Download service account key from Firebase Console
    # Project Settings → Service Accounts → Generate new private key
    # Save it as scripts/serviceAccountKey.json
    node scripts/seedData.js
    ```

12. **Run the development server**
    ```bash
    npm run dev
    ```

    The app will open at `http://localhost:3000`

## Project Structure

```
├── public/                 # Static assets
├── src/
│   ├── components/        # Reusable components
│   │   ├── CreateReportForm.jsx
│   │   ├── MapView.jsx
│   │   ├── ProtectedRoute.jsx
│   │   ├── Login.js
│   │   ├── Signup.js
│   │   └── ...
│   ├── pages/            # Page components
│   │   ├── CitizenDashboard.jsx
│   │   ├── PublicMap.jsx
│   │   ├── ReportDetail.jsx
│   │   └── admin/
│   │       ├── AdminDashboard.jsx
│   │       └── AdminAnalytics.jsx
│   ├── layouts/          # Layout components
│   │   └── MainLayout.jsx
│   ├── hooks/            # Custom React hooks
│   │   ├── useReports.js
│   │   └── useGeolocation.js
│   ├── services/         # Firebase service functions
│   │   ├── reportService.js
│   │   ├── storageService.js
│   │   ├── commentService.js
│   │   └── upvoteService.js
│   ├── context/          # React context providers
│   │   └── AuthContext.js
│   ├── utils/            # Utility functions and constants
│   │   └── constants.js
│   ├── firebase.js       # Firebase configuration
│   ├── App.jsx           # Main app component
│   └── main.jsx          # Entry point
├── functions/            # Cloud Functions
│   ├── index.js
│   └── package.json
├── firestore.rules       # Firestore security rules
├── storage.rules         # Storage security rules
├── firestore.indexes.json # Firestore indexes
├── firebase.json         # Firebase configuration
└── package.json
```

## Data Model

### Collections

**users**
```javascript
{
  id: string,
  name: string,
  email: string,
  role: 'citizen' | 'admin',
  fcmToken?: string, // For push notifications
  createdAt: Timestamp
}
```

**reports**
```javascript
{
  id: string,
  userId: string,
  title: string,
  description: string,
  category: string,
  status: 'pending' | 'in-progress' | 'resolved',
  coordinates: { lat: number, lng: number },
  photoUrls: string[],
  department?: string,
  adminNotes?: string,
  createdAt: Timestamp,
  updatedAt: Timestamp,
  verified: boolean
}
```

**comments**
```javascript
{
  id: string,
  reportId: string,
  userId: string,
  text: string,
  createdAt: Timestamp
}
```

**upvotes**
```javascript
{
  id: string (format: "reportId_userId"),
  reportId: string,
  userId: string,
  createdAt: Timestamp
}
```

## Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run lint` - Run ESLint

## Deployment

### Deploy to Firebase Hosting

1. **Build the app**
   ```bash
   npm run build
   ```

2. **Deploy**
   ```bash
   firebase deploy
   ```

   Or deploy specific services:
   ```bash
   firebase deploy --only hosting
   firebase deploy --only functions
   firebase deploy --only firestore:rules
   ```

## Security Notes

- Firestore rules enforce that users can only edit their own reports
- Admins have full access to all data
- Image uploads are restricted to 5MB for reports, 2MB for profiles
- Only authenticated users can access the application
- Cloud Functions validate permissions server-side

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

MIT License - feel free to use this project for learning or commercial purposes.

## Support

For issues or questions, please open an issue on the repository.

## Acknowledgments

- Built with React, Firebase, Material UI, and Leaflet
- Icons from Material UI Icons
- Maps powered by OpenStreetMap
