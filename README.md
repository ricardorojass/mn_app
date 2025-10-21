# MN App

Web application for managing international money transfers with automated receipt generation.

## Features

- 👥 Sender and recipient management with relationship tracking
- 💸 Transfer form with real-time exchange rate calculations
- 🏦 Bank account management for recipients
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

1. Admin logs in
2. Navigate to "New Transfer"
3. Type sender's name or phone in search input
4. Submit search
5. System gets existing sender OR shows form to create new sender
6. If sender has recipients:
   - Second part of form appears with recipient selector
   - Third part shows bank account selector
7. If sender has NO recipients:
   - Second part of form shows empty fields to create new recipient
   - Third part shows fields to add bank account
8. Select recipient and bank account (if existing)
9. Enter amount and exchange rate
10. System calculates totals automatically (amount received, total charged)
11. Submit form to create transfer
12. Redirect to receipt page with shareable URL

### Sharing Receipt

After creating transfer:
- Click "Share via WhatsApp" to send directly
- Click "Download Image" to save receipt
- Copy URL to share manually

## Database Schema


**Users (Admin/Operators)**
- Authentication and role management
- Creates senders and transfers

**Senders**
- People who send money (pay in cash)
- Has many recipients
- Located in USA typically

**Recipients**
- People who receive money
- Belong to a sender (1 sender → many recipients)
- Located in Colombia typically
- Has many bank accounts

**Recipient Bank Accounts**
- Bank details for receiving transfers
- Multiple accounts per recipient supported
- One marked as default

**Transfers**
- Transaction records
- Links sender → recipient → bank account
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