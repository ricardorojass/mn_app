# Development Checklist

## Phase 1: MVP (Week 1)

### Day 1: Project Setup
- [ ] Create Rails app: `rails new money_transfer_app --database=postgresql --css=tailwind`
- [ ] Initialize Git repository
- [ ] Create GitHub repository
- [ ] Setup PostgreSQL database
- [ ] Configure Tailwind CSS
- [ ] Deploy staging environment to (to define)
- [ ] Setup `.env` file for environment variables

### Day 2: Database Models
- [ ] Generate Client model (name, phone, email, id_number, id_type, address, notes)
- [ ] Generate BankAccount model (client_id, bank_name, account_number, account_type, account_holder_name, is_default)
- [ ] Generate Transfer model (transfer_number, sender_id, recipient_id, sender_bank_account_id, recipient_bank_account_id, amount_sent, currency_sent, exchange_rate, amount_received, currency_received, fee, total_charged, delivery_method, status, notes, completed_at)
- [ ] Add model associations (has_many, belongs_to)
- [ ] Add validations to models
- [ ] Add before_save callbacks (normalize phone, generate transfer number, calculate amounts)
- [ ] Create seed data (3-5 test customers with bank accounts)
- [ ] Run migrations and test in console

### Day 3: Routes & Controllers
- [ ] Define routes in `config/routes.rb`
- [ ] Generate TransfersController (new, create, show, index, receipt)
- [ ] Generate ClientsController (index, new, create, show)
- [ ] Generate BankAccountsController (new, create)
- [ ] Create API namespace for AJAX endpoints
- [ ] Generate Api::V1::ClientsController (index, bank_accounts)
- [ ] Test routes: `rails routes | grep transfer`

### Day 4: Transfer Form (Frontend)
- [ ] Create transfer form view (`app/views/transfers/new.html.erb`)
- [ ] Two-column layout (Sender | Recipient)
- [ ] Add customer dropdowns with Tailwind styling
- [ ] Add bank account dropdowns (hidden initially)
- [ ] Add amount, exchange rate, fee inputs
- [ ] Add delivery method selector
- [ ] Add notes textarea
- [ ] Display real-time calculation summary
- [ ] Add form validation errors display
- [ ] Make form mobile-responsive

### Day 5: Dynamic Form Behavior (Stimulus)
- [ ] Generate Stimulus controller: `rails g stimulus transfer-form`
- [ ] Implement `loadSenderAccounts()` function
- [ ] Implement `loadRecipientAccounts()` function
- [ ] Implement `calculateTotal()` function
- [ ] Add targets for all form elements
- [ ] Add actions for change/input events
- [ ] Test AJAX calls to API endpoints
- [ ] Add loading states/spinners
- [ ] Handle errors gracefully

### Day 6: Receipt Page
- [ ] Create receipt layout (`app/views/layouts/receipt.html.erb`)
- [ ] Design receipt view (`app/views/transfers/receipt.html.erb`)
- [ ] Add professional styling (white card, centered)
- [ ] Display all transfer information
- [ ] Add status indicator (completed)
- [ ] Add "Share via WhatsApp" button
- [ ] Add "Download as Image" button
- [ ] Include html2canvas library
- [ ] Implement WhatsApp share function (opens wa.me with message)
- [ ] Implement download image function
- [ ] Test on mobile device

### Day 7: Transfer List & Polish
- [ ] Create transfer index view
- [ ] Add table with transfers (date, sender, recipient, amount, status)
- [ ] Add search by transfer ID
- [ ] Add date filter
- [ ] Add pagination (25 per page)
- [ ] Add "View Receipt" links
- [ ] Style with Tailwind
- [ ] Add navigation menu (New Transfer | Transfer History)
- [ ] Improve error messages
- [ ] Add success flash messages
- [ ] Final styling polish

### Day 8: Testing & Deploy
- [ ] Manual testing of all flows
- [ ] Test on mobile browser
- [ ] Test WhatsApp sharing
- [ ] Test image download
- [ ] Fix any bugs found
- [ ] Update seed data with realistic examples
- [ ] Deploy to production (to define)
- [ ] Run migrations on production
- [ ] Test production environment
- [ ] Create backup of database

---

## Phase 2: Enhancements (Week 2)

### Customer Management
- [ ] Create customers index page with search
- [ ] Add "New Customer" form
- [ ] Add "Edit Customer" functionality
- [ ] Add "View Customer" detail page with transfer history
- [ ] Add bank account management (add/edit/delete)
- [ ] Validate phone number format
- [ ] Add customer notes field

### Reporting
- [ ] Create reports page
- [ ] Monthly summary (total transfers, total amount, avg exchange rate)
- [ ] Top customers report
- [ ] Transfer by delivery method chart
- [ ] Export to CSV functionality
- [ ] Date range selector

### Search & Filters
- [ ] Advanced search (by customer name, phone, transfer ID)
- [ ] Filter by status
- [ ] Filter by delivery method
- [ ] Filter by date range
- [ ] Sort columns (amount, date, customer)

### UI Improvements
- [ ] Add dashboard/home page with stats
- [ ] Improve mobile experience
- [ ] Add keyboard shortcuts (Ctrl+N for new transfer)
- [ ] Add tooltips for fields
- [ ] Improve loading states
- [ ] Add confirmation dialogs

---

## Phase 3: Automation (Month 2)

### WhatsApp API Integration
- [ ] Sign up for Twilio account
- [ ] Get WhatsApp Business API access
- [ ] Install twilio-ruby gem
- [ ] Create WhatsApp service class
- [ ] Add "Send Receipt" button to transfer show page
- [ ] Implement automated receipt sending
- [ ] Add delivery status tracking
- [ ] Handle failures gracefully

### Notifications
- [ ] Send SMS confirmation to sender
- [ ] Send SMS notification to recipient
- [ ] Email receipt as backup
- [ ] Add notification preferences per customer

### Background Jobs
- [ ] Setup Solid Queue (built-in Rails 8)
- [ ] Move receipt generation to background job
- [ ] Move WhatsApp sending to background job
- [ ] Add job status indicators

---

## Phase 4: Advanced Features (Month 3+)

### Authentication & Authorization
- [ ] Add Devise gem
- [ ] Create User model
- [ ] Add login/logout
- [ ] Add role-based permissions (admin, operator)
- [ ] Restrict receipt URLs to logged-in users (optional)
- [ ] Add audit trail

### Customer Portal
- [ ] Create customer authentication
- [ ] Customer can view their transfer history
- [ ] Customer can download receipts
- [ ] Customer can update their bank accounts

### Analytics
- [ ] Add charting library (Chart.js)
- [ ] Transfers over time graph
- [ ] Revenue tracking
- [ ] Exchange rate trends
- [ ] Customer growth chart

### API
- [ ] Create REST API for mobile app
- [ ] Add API authentication (JWT)
- [ ] Document API with Swagger
- [ ] Rate limiting

---

## Ongoing Tasks

### Maintenance
- [ ] Weekly database backups
- [ ] Monitor error logs
- [ ] Update dependencies monthly
- [ ] Performance optimization
- [ ] Security updates

### Documentation
- [ ] Keep README updated
- [ ] Document deployment process
- [ ] Create user guide
- [ ] Create operator training materials

---

## Bugs / Issues
(Track in GitHub Issues instead)

---

## Ideas / Future Considerations
- Multi-currency support (EUR, GBP)
- Real-time exchange rate API integration
- Integration with accounting software (QuickBooks)
- Mobile app (React Native)
- Franchise/multi-location support
- KYC/AML compliance automation
- Customer referral program
- Loyalty points system