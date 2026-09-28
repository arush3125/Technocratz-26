# Environment Variables Setup Guide

Create a `.env.local` file in the project root with the following variables:

## Required Variables

```env
# UPI Payment Configuration
NEXT_PUBLIC_UPI_ID=your-college-upi-id@upi
NEXT_PUBLIC_PAYEE_NAME=Agnel Polytechnic Vashi
```

## Optional Variables (for Google Sheets Integration)

### Option 1: Google Apps Script Webhook (Recommended)

```env
NEXT_PUBLIC_GOOGLE_SCRIPT_WEBHOOK_URL=your-google-script-webhook-url
```

To set up Google Apps Script:
1. Create a new Google Sheet
2. Go to Extensions > Apps Script
3. Paste the following code:

```javascript
function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Registrations');
    
    if (!sheet) {
      sheet = SpreadsheetApp.getActiveSpreadsheet().insertSheet('Registrations');
      sheet.appendRow([
        'Registration ID', 'Timestamp', 'Name', 'Year', 'Branch',
        'College', 'Contact', 'Email', 'Competition ID', 'Competition Title',
        'Fee', 'Transaction ID', 'Payment Method', 'Payment Status'
      ]);
    }
    
    sheet.appendRow([
      data.registrationId, data.timestamp, data.name, data.year, data.branch,
      data.college, data.contact, data.email, data.competitionId, data.competitionTitle,
      data.fee, data.transactionId, data.paymentMethod, data.paymentStatus
    ]);
    
    return ContentService.createTextOutput(JSON.stringify({success: true}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({success: false, error: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. Deploy as Web App:
   - Click Deploy > New Deployment
   - Select type: Web app
   - Execute as: Me
   - Who has access: Anyone
   - Copy the web app URL

### Option 2: Direct Google Sheets API

```env
NEXT_PUBLIC_GOOGLE_SHEETS_ID=your-spreadsheet-id
NEXT_PUBLIC_GOOGLE_PROJECT_ID=your-project-id
NEXT_PUBLIC_GOOGLE_PRIVATE_KEY_ID=your-private-key-id
NEXT_PUBLIC_GOOGLE_PRIVATE_KEY=your-private-key
NEXT_PUBLIC_GOOGLE_CLIENT_EMAIL=your-client-email
NEXT_PUBLIC_GOOGLE_CLIENT_ID=your-client-id
```

## Fallback Behavior

If no Google Sheets integration is configured, the system will automatically:
- Store registration data in browser localStorage
- Provide CSV export functionality for manual data transfer
- Continue to work without any backend

## Testing

1. Update the UPI ID with your actual college UPI ID
2. Test the registration flow with a small amount
3. Verify data is being stored correctly
4. For production, set up Google Sheets integration
