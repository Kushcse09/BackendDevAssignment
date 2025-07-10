# Backend Assignment - User Data Management & Twitter OAuth API

A comprehensive Symfony-based backend solution implementing user data management with CSV upload functionality, database backup/restore operations, and Twitter OAuth integration.

## 🚀 Features

### Part 1: User Data Management API
- **CSV Upload & Processing** - Upload and process CSV files with comprehensive validation
- **Asynchronous Email Notifications** - Queue-based email system for user notifications
- **Database Operations** - Full CRUD operations with pagination support
- **Backup & Restore System** - Automated database backup and restore functionality
- **API Authentication** - Role-based access control with API keys

### Part 2: Twitter OAuth Integration
- **OAuth 1.0a Implementation** - Complete Twitter authentication flow
- **User Profile Management** - Store and manage Twitter user profiles
- **Session Handling** - Secure session management for web applications
- **API Endpoints** - RESTful endpoints for mobile app integration

## 🛠️ Technology Stack

- **Framework**: Symfony 6.3
- **Language**: PHP 8.1+
- **Database**: MySQL 8.0+
- **Queue System**: Symfony Messenger with Doctrine transport
- **Email**: Symfony Mailer with async processing
- **Authentication**: Custom API key authenticator + Twitter OAuth 1.0a
- **Frontend**: Bootstrap 5 with Twig templates

## 📋 Requirements

- PHP 8.1 or higher
- Composer
- MySQL 8.0 or higher
- Node.js (for frontend dependencies)
- Twitter Developer Account (for OAuth)

## 🔧 Installation

### 1. Clone the Repository
```bash
git clone <repository-url>
cd backend-assignment
```

### 2. Install Dependencies
```bash
composer install
```

### 3. Environment Configuration
```bash
cp .env.example .env
```

Edit the `.env` file and configure:
- Database connection (`DATABASE_URL`)
- Twitter API credentials
- Mailer configuration
- API keys

### 4. Database Setup
```bash
# Create database
php bin/console doctrine:database:create

# Run migrations
php bin/console doctrine:migrations:migrate

# Load sample data (optional)
php bin/console app:seed-database
```

### 5. Start the Application
```bash
# Start Symfony server
symfony server:start

# Start message worker (in separate terminal)
php bin/console messenger:consume async
```

## 🔑 API Authentication

The API uses API key authentication with two access levels:

- **Admin Key**: `admin_secret_key_123` (full access)
- **User Key**: `user_secret_key_456` (read-only access)

Include the API key in the request header:
```
X-API-KEY: your_api_key_here
```

## 📡 API Endpoints

### User Management

#### Upload CSV Data
```http
POST /api/upload
Content-Type: multipart/form-data
X-API-KEY: admin_secret_key_123

Body: file (CSV file)
```

#### Get Users
```http
GET /api/users?page=1&limit=10
X-API-KEY: user_secret_key_456
```

### Database Management

#### Create Backup
```http
GET /api/backup
X-API-KEY: admin_secret_key_123
```

#### Restore Database
```http
POST /api/restore
Content-Type: multipart/form-data
X-API-KEY: admin_secret_key_123

Body: backup_file (SQL file)
```

#### List Backups
```http
GET /api/backups
X-API-KEY: admin_secret_key_123
```

### Twitter OAuth

#### Initiate Authentication
```http
GET /auth/twitter
Accept: application/json
```

#### Handle Callback
```http
GET /auth/twitter/callback?oauth_token=...&oauth_verifier=...
```

## 📊 CSV File Format

The CSV file should contain the following columns:
- `name` - User's full name
- `email` - User's email address (must be unique)
- `username` - User's username (must be unique)
- `address` - User's address
- `role` - User's role (USER or ADMIN)

### Example CSV:
```csv
name,email,username,address,role
John Doe,john.doe@example.com,johndoe,"123 Main St, City, State 12345",USER
Jane Smith,jane.smith@example.com,janesmith,"456 Oak Ave, City, State 67890",ADMIN
```

## 🔐 Twitter OAuth Configuration

1. Create a Twitter Developer Account
2. Create a new Twitter App
3. Generate Consumer Keys and Access Tokens
4. Configure in `.env` file:

```env
TWITTER_CONSUMER_KEY=your_consumer_key
TWITTER_CONSUMER_SECRET=your_consumer_secret
TWITTER_ACCESS_TOKEN=your_access_token
TWITTER_ACCESS_TOKEN_SECRET=your_access_token_secret
TWITTER_CALLBACK_URL=http://localhost:8000/auth/twitter/callback
```

## 📧 Email Configuration

For development, use MailHog for testing emails:

```bash
# Install MailHog
brew install mailhog  # macOS
# or
sudo apt-get install mailhog  # Ubuntu

# Start MailHog
mailhog
```

Configure in `.env`:
```env
MAILER_DSN=smtp://localhost:1025
```

## 🧪 Testing with Postman

1. Import the collection: `postman/Backend_Assignment_API.postman_collection.json`
2. Import the environment: `postman/Backend_Assignment.postman_environment.json`
3. Update environment variables as needed
4. Test all endpoints

## 📁 Project Structure

```
backend-assignment/
├── src/
│   ├── Controller/          # API and web controllers
│   ├── Entity/             # Database entities
│   ├── Repository/         # Data access layer
│   ├── Service/            # Business logic
│   ├── Security/           # Authentication
│   ├── Message/            # Async messages
│   └── MessageHandler/     # Message handlers
├── templates/              # Twig templates
├── config/                 # Configuration files
├── migrations/             # Database migrations
├── data/                   # Sample data
├── postman/               # Postman collection
└── var/                   # Cache, logs, backups
```

## 🔄 Async Email Processing

Emails are processed asynchronously using Symfony Messenger:

```bash
# Start the message worker
php bin/console messenger:consume async

# Check message queue status
php bin/console messenger:stats
```

## 💾 Database Backup & Restore

### Automatic Backups
Backups are created automatically and stored in `var/backups/`:
- Filename format: `backup_YYYY-MM-DD_HH-mm-ss.sql`
- Include timestamp and file size information
- Verification of backup integrity

### Manual Backup
```bash
# Create backup via CLI
php bin/console app:backup-database

# Or via API
curl -X GET http://localhost:8000/api/backup   -H "X-API-KEY: admin_secret_key_123"
```

## 🚨 Error Handling

The application includes comprehensive error handling:
- Validation errors with detailed messages
- Database connection errors
- File upload errors
- OAuth authentication errors
- Email sending errors

## 🔒 Security Features

- API key authentication
- Input validation and sanitization
- SQL injection prevention
- CSRF protection
- Secure OAuth token storage
- File upload security checks

## 📈 Performance Optimizations

- Database query optimization
- Async email processing
- Efficient CSV parsing
- Pagination for large datasets
- Connection pooling

## 🐛 Troubleshooting

### Common Issues

1. **Database Connection Error**
   - Check MySQL service is running
   - Verify database credentials in `.env`
   - Ensure database exists

2. **Email Not Sending**
   - Check MAILER_DSN configuration
   - Verify message worker is running
   - Check logs in `var/log/`

3. **Twitter OAuth Failing**
   - Verify Twitter API credentials
   - Check callback URL configuration
   - Ensure proper permissions in Twitter App

4. **CSV Upload Issues**
   - Check file format and encoding
   - Verify file size limits
   - Ensure proper column headers

### Logs Location
- Application logs: `var/log/dev.log`
- Email logs: Check message worker output
- Database logs: Check MySQL error logs

## 📞 Support

For issues or questions:
1. Check the troubleshooting section
2. Review application logs
3. Test with provided Postman collection
4. Verify environment configuration

## 🎯 Example API Responses

### Successful CSV Upload
```json
{
    "success": true,
    "total_records": 10,
    "processed": 9,
    "skipped": 1,
    "errors": [
        {
            "row": 5,
            "error": "Email already exists",
            "data": {...}
        }
    ]
}
```

### User List Response
```json
{
    "success": true,
    "data": [
        {
            "id": 1,
            "name": "John Doe",
            "email": "john.doe@example.com",
            "username": "johndoe",
            "address": "123 Main St, City, State 12345",
            "role": "USER",
            "created_at": "2024-12-07 10:00:00"
        }
    ],
    "pagination": {
        "page": 1,
        "limit": 10,
        "total": 100,
        "total_pages": 10
    }
}
```

### Twitter Auth Response
```json
{
    "success": true,
    "user": {
        "id": 1,
        "twitter_id": "123456789",
        "name": "John Doe",
        "username": "johndoe",
        "email": "john@example.com",
        "followers_count": 150,
        "following_count": 75
    }
}
```

## 🏆 Best Practices Implemented

- **SOLID Principles**: Clean architecture with dependency injection
- **PSR Standards**: Following PHP-FIG standards
- **Security**: Multiple layers of security validation
- **Error Handling**: Comprehensive error handling and logging
- **Documentation**: Detailed API documentation and examples
- **Testing**: Ready-to-use Postman collection for testing

---

**Note**: This is a demonstration project for educational purposes. For production use, additional security measures, monitoring, and optimization should be implemented.
