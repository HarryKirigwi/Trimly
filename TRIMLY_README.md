# Trimly - Smart Appointment & Client Management System

## Overview
Trimly is a comprehensive booking and client management platform designed specifically for Denis' Barbershop in Nairobi, Kenya. The system streamlines appointment scheduling, walk-in queue management, client tracking, and business analytics.

## Features

### 1. Dashboard
- **Real-time Statistics**: Today's bookings, walk-ins, revenue, and total clients
- **Today's Schedule**: Quick view of upcoming appointments
- **Quick Actions**: Fast access to common tasks (new appointments, add walk-ins, register clients)

### 2. Booking Calendar
- **Visual Time Slots**: Interactive calendar with 30-minute time slots from 9:00 AM to 7:30 PM
- **Date Selection**: Calendar picker for viewing bookings on any date
- **Smart Booking**: 
  - Select from existing clients or create new ones
  - Choose from predefined services with pricing
  - Automatic SMS notifications sent to clients upon booking
- **Search Functionality**: Find bookings quickly

### 3. Walk-in Queue Management
- **Digital Queue**: Automated queue system for walk-in customers
- **Estimated Wait Times**: Automatically calculated based on queue position
- **Status Tracking**: 
  - Waiting
  - In Progress
  - Completed
- **Service Management**: Start and complete services with one click
- **Payment Tracking**: Automatic revenue calculation

### 4. Client Management
- **Client Database**: Complete customer information storage
  - Name, phone, email
  - Total visits and spending
  - Favorite services
  - Last visit tracking
- **Client Classification**:
  - VIP: 30+ visits
  - Regular: 10-29 visits
  - New: 0-9 visits
- **Search & Filter**: Find clients by name, phone, or email
- **Client History**: View detailed appointment history for each client
- **Quick Actions**: Book appointments, send messages, view history

### 5. Reports & Analytics
- **Revenue Tracking**: Daily, weekly, monthly, and all-time revenue
- **Booking Statistics**: Total bookings, completion rates, average booking value
- **Service Analytics**: Most popular services with visual breakdown
- **Data Export**: CSV export for appointments and client database
- **Quick Stats**: Walk-ins, VIP clients, regular clients

## Technical Implementation

### Frontend
- **React 18.3.1** with TypeScript
- **Tailwind CSS v4** for styling
- **Shadcn/ui** component library
- **React Context API** for state management
- **localStorage** for data persistence

### Data Management
- **Local Storage Persistence**: All data saved to browser localStorage
- **Real-time Updates**: Instant UI updates across all components
- **Auto-save**: Changes saved automatically

### Services & Pricing
1. **Haircut** - KES 600 (30 min)
2. **Beard Trim** - KES 400 (20 min)
3. **Beard Shave** - KES 300 (15 min)
4. **Haircut + Beard Trim** - KES 800 (45 min)
5. **Haircut + Styling** - KES 1,000 (60 min)
6. **Kids Haircut** - KES 400 (25 min)

## Notifications System (Mock Implementation)

The current version includes mock notification functions that log to console. For production deployment:

### SMS Integration (Recommended: Africa's Talking for Kenya)
```typescript
// Example integration with Africa's Talking
import AfricasTalking from 'africastalking';

const africastalking = AfricasTalking({
  apiKey: 'YOUR_API_KEY',
  username: 'YOUR_USERNAME'
});

const sms = africastalking.SMS;

async function sendSMS(phone: string, message: string) {
  const result = await sms.send({
    to: [phone],
    message: message
  });
  return result;
}
```

### Email Integration (Recommended: SendGrid or Resend)
```typescript
// Example integration with Resend
import { Resend } from 'resend';

const resend = new Resend('YOUR_API_KEY');

async function sendEmail(to: string, subject: string, html: string) {
  const result = await resend.emails.send({
    from: 'Denis Barbershop <bookings@denisbarbershop.com>',
    to: [to],
    subject: subject,
    html: html
  });
  return result;
}
```

## Calendar Integration (Future Enhancement)

To integrate with Google Calendar:

1. **Enable Google Calendar API** in Google Cloud Console
2. **Add OAuth2 credentials**
3. **Install Google API client**:
   ```bash
   pnpm add googleapis
   ```
4. **Implement calendar sync**:
   ```typescript
   import { google } from 'googleapis';
   
   const calendar = google.calendar('v3');
   
   async function createCalendarEvent(appointment) {
     const event = {
       summary: `${appointment.clientName} - ${appointment.service}`,
       description: `Barbershop appointment for ${appointment.clientName}`,
       start: {
         dateTime: `${appointment.date}T${convertTo24Hour(appointment.time)}:00`,
         timeZone: 'Africa/Nairobi',
       },
       end: {
         dateTime: calculateEndTime(appointment),
         timeZone: 'Africa/Nairobi',
       },
       reminders: {
         useDefault: false,
         overrides: [
           { method: 'sms', minutes: 60 },
           { method: 'email', minutes: 120 },
         ],
       },
     };
     
     return await calendar.events.insert({
       calendarId: 'primary',
       resource: event,
     });
   }
   ```

## Deployment Options

### Option 1: Vercel (Recommended for React Apps)
1. Push code to GitHub
2. Connect repository to Vercel
3. Deploy with one click
4. Custom domain: `trimly.denisbarbershop.com`

### Option 2: Netlify
1. Push code to GitHub
2. Connect to Netlify
3. Configure build settings
4. Deploy

### Option 3: Firebase Hosting
1. Install Firebase CLI: `npm install -g firebase-tools`
2. Initialize Firebase: `firebase init`
3. Build: `pnpm run build`
4. Deploy: `firebase deploy`

## Database Migration (Future Production Use)

For production with Django REST Framework backend:

### PostgreSQL Schema
```sql
-- Clients Table
CREATE TABLE clients (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(20) UNIQUE NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  total_visits INTEGER DEFAULT 0,
  total_spent DECIMAL(10, 2) DEFAULT 0,
  favorite_service VARCHAR(50),
  status VARCHAR(20),
  last_visit TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Appointments Table
CREATE TABLE appointments (
  id SERIAL PRIMARY KEY,
  client_id INTEGER REFERENCES clients(id),
  date DATE NOT NULL,
  time TIME NOT NULL,
  service VARCHAR(50) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  status VARCHAR(20) DEFAULT 'pending',
  notes TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Walk-ins Table
CREATE TABLE walkins (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(20),
  service VARCHAR(50) NOT NULL,
  status VARCHAR(20) DEFAULT 'waiting',
  arrival_time TIMESTAMP DEFAULT NOW(),
  estimated_wait INTEGER,
  price DECIMAL(10, 2),
  completed_at TIMESTAMP
);
```

## Usage Guide

### For Denis (Shop Owner)
1. **Morning**: Check today's dashboard for scheduled appointments
2. **Walk-ins**: Add customers to queue as they arrive
3. **Managing Queue**: Start service → Complete service → Collect payment
4. **End of Day**: Review reports for daily revenue and statistics

### For Clients
Future mobile app features:
- Book appointments online
- View available time slots
- Receive SMS/email reminders
- Track loyalty points (VIP status)

## Data Persistence

Current implementation uses **localStorage** which:
- ✅ Persists data across browser sessions
- ✅ Works offline
- ✅ No server required for MVP
- ⚠️ Data is local to one device/browser
- ⚠️ Clearing browser cache deletes data

For multi-device access, implement backend API with PostgreSQL.

## Support & Maintenance

### Common Tasks
- **Clear all data**: `localStorage.clear()` in browser console
- **Backup data**: Use export feature in Reports tab
- **Import data**: Manual process (future feature)

### Troubleshooting
- **Data not saving**: Check browser localStorage is enabled
- **Notifications not sending**: Check console logs for mock output
- **Calendar issues**: Ensure date is selected

## Future Enhancements

1. **Backend Integration**
   - Django REST API
   - PostgreSQL database
   - User authentication

2. **Mobile App**
   - React Native app for clients
   - Push notifications
   - Mobile booking

3. **Payment Integration**
   - M-Pesa integration for Kenya
   - Payment receipts via SMS
   - Digital payment tracking

4. **Advanced Features**
   - Barber staff management
   - Multi-chair scheduling
   - Inventory management
   - Customer loyalty program
   - Automated marketing campaigns

## License
Proprietary - Denis' Barbershop

## Contact
Denis' Barbershop, Nairobi, Kenya  
Phone: +254 712 345 678  
Email: info@denisbarbershop.com

---

Built with ❤️ for Denis' Barbershop using React + Tailwind CSS
