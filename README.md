# MN App

Web application for managing international money transfers with automated receipt generation.

## Features

- 👥 Customer database with bank account management
- 💸 Transfer form with real-time exchange rate calculations
- 🧾 Professional receipt pages with shareable links
- 📱 WhatsApp sharing capability
- 📊 Transfer history and search
- 📈 Monthly reports

## Tech Stack

- **Backend:** Ruby on Rails 8
- **Database:** PostgreSQL
- **Frontend:** Tailwind CSS + Hotwire (Turbo + Stimulus)
- **Hosting:** To define

## Prerequisites

- Ruby 3.2+
- Rails 8.0+
- PostgreSQL 14+
- Node.js 18+ (for asset compilation)

## Quick Start
```bash
# Clone the repository
git clone https://github.com/yourusername/mn_app.git
cd mn_app

# Install dependencies
bundle install

# Setup database
rails db:create db:migrate db:seed

# Start server
rails server
```

Visit: http://localhost:3000

## Usage

### Creating a Transfer

1. Navigate to "New Transfer"
2. Select sender from dropdown (or create new customer)
3. Select recipient from dropdown
4. Choose bank accounts
5. Enter amount and exchange rate
6. System calculates totals automatically
7. Submit to generate receipt

### Sharing Receipt

After creating transfer:
- Click "Share via WhatsApp" to send directly
- Click "Download Image" to save receipt
- Copy URL to share manually

## Database Schema

**Clients**
- Name, phone, email, ID number
- Has many bank accounts

**Bank Accounts**
- Bank name, account number, account type
- Belongs to client

**Transfers**
- Sender/recipient (clients)
- Amount, exchange rate, fee
- Status, delivery method
- Auto-generated transfer ID (TRF-YYYY-NNNN)

## Development
```bash
# Run tests
rails test

# Run migrations
rails db:migrate

# Console
rails console

# Check routes
rails routes
```

## Deployment
```bash
# Deploy to Render.com
git push origin main

# Run migrations on production
rails db:migrate RAILS_ENV=production
```

## Environment Variables

## Contributing

This is a private business application. Internal team only.

## License

Proprietary - All rights reserved

## Support

For issues or questions, contact: mntranferco@gmail.com