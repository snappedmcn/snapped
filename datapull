from flask import Flask, redirect, request, session
from requests_oauthlib import OAuth2Session
import json
import gspread
from oauth2client.service_account import ServiceAccountCredentials

# Define Flask app
app = Flask(__name__)
app.secret_key = 'your_secret_key'  # Change this to a secure secret key

# Define Snapchat OAuth configuration
CLIENT_ID = 'your_snapchat_client_id'  # Replace with your Snapchat client ID
CLIENT_SECRET = 'your_snapchat_client_secret'  # Replace with your Snapchat client secret
AUTHORIZATION_BASE_URL = 'https://accounts.snapchat.com/login/oauth2/authorize'
TOKEN_URL = 'https://accounts.snapchat.com/login/oauth2/access_token'
SCOPE = ['https://auth.snapchat.com/oauth2/api/user.display_name']

# Define Google Sheets credentials
SCOPE_SHEETS = ['https://www.googleapis.com/auth/spreadsheets']
CREDS_FILENAME = 'your_google_sheets_credentials.json'  # Replace with your JSON credentials file path
SHEET_ID = 'your_google_sheet_id'  # Replace with your Google Sheet ID

# Define the callback route for Snapchat authentication
@app.route('/callback')
def callback():
    # Get access token from Snapchat
    snapchat = OAuth2Session(CLIENT_ID, redirect_uri='http://localhost:5000/callback')
    token = snapchat.fetch_token(TOKEN_URL, client_secret=CLIENT_SECRET, authorization_response=request.url)

    # Store access token in session
    session['snapchat_token'] = token

    # Redirect to route for exporting data to Google Sheets
    return redirect('/export')

# Define route for exporting data to Google Sheets
@app.route('/export')
def export_to_sheets():
    # Retrieve access token from session
    token = session.get('snapchat_token')

    # Retrieve data from Snapchat API (example: fetching user display name)
    snapchat = OAuth2Session(CLIENT_ID, token=token)
    user_data = snapchat.get('https://kit.snapchat.com/v1/me').json()
    display_name = user_data.get('display_name')

    # Authenticate with Google Sheets
    credentials = ServiceAccountCredentials.from_json_keyfile_name(CREDS_FILENAME, SCOPE_SHEETS)
    client = gspread.authorize(credentials)

    # Open Google Sheet
    sheet = client.open_by_key(SHEET_ID).sheet1

    # Example: Write data to Google Sheet
    sheet.append_row([display_name])

    return 'Data exported to Google Sheets successfully'

if __name__ == '__main__':
    app.run(debug=True)
