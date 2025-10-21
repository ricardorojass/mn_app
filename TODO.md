# Development Checklist

## Phase 1: MVP (Week 1)

### Day 1: Project Setup
- [ ] Create Rails app
- [ ] Initialize Git repository
- [ ] Create GitHub repository
- [ ] Setup PostgreSQL database
- [ ] Configure Tailwind CSS
- [ ] Deploy staging environment to (to define)
- [ ] Setup `.env` file for environment variables

### Day 2: Database Models & Migrations

**Generate Models**
```bash
rails g model User email:string encrypted_password:string role:string full_name:string phone:string active:boolean

rails g model Sender full_name:string phone:string email:string id_type:string id_number:string country:string address:text city:string state:string notes:text is_active:boolean created_by:references

rails g model Recipient sender:references full_name:string phone:string email:string id_type:string id_number:string country:string address:text city:string relationship:string notes:text is_active:boolean is_favorite:boolean created_by:references

rails g model RecipientBankAccount recipient:references bank_name:string account_holder_name:string account_number:string account_type:string bank_code:string currency:string is_default:boolean is_active:boolean

rails g model Transfer transfer_number:string sender:references recipient:references recipient_bank_account:references amount_sent:decimal exchange_rate:decimal amount_received:decimal fee_amount:decimal total_charged:decimal currency_sent:string currency_received:string payment_method:string delivery_method:string purpose:string status:string completed_at:datetime notes:text created_by:references
```

**Tasks:**
- [ ] Generate all 5 models with commands above
- [ ] Edit migrations to add:
  - Decimal precisions: `amount_sent:decimal{12,2}`
  - NOT NULL constraints
  - DEFAULT values
  - CHECK constraints (amount > 0)
  - UNIQUE constraints
- [ ] Add indexes to migrations:
  - `senders(phone)`, `senders(full_name)`, `senders(is_active)`
  - `recipients(sender_id)`, `recipients(phone)`, `recipients(is_active)`
  - `recipient_bank_accounts(recipient_id)`, `recipient_bank_accounts(is_default)`
  - `transfers(transfer_number)`, `transfers(sender_id)`, `transfers(recipient_id)`, `transfers(created_at)`
- [ ] Run migrations: `rails db:migrate`
- [ ] Add model associations (has_many, belongs_to)
- [ ] Add validations to all models
- [ ] Add scopes (active, recent, etc.)
- [ ] Add callbacks:
  - `Sender`: normalize phone before save
  - `RecipientBankAccount`: ensure single default per recipient
  - `Transfer`: generate transfer_number, calculate amounts
- [ ] Create seed data in `db/seeds.rb`:
  - 1 admin user
  - 2-3 senders with phone numbers
  - 3-5 recipients per sender
  - 1-2 bank accounts per recipient
  - 5-10 sample transfers
- [ ] Run seeds: `rails db:seed`
- [ ] Test in console: `rails c`

### Day 3: Routes & Controllers

**Define Routes:**
```ruby
# config/routes.rb
Rails.application.routes.draw do
  root 'transfers#new'
  
  resources :transfers, only: [:new, :create, :show, :index]
  resources :senders, only: [:index, :show]
  resources :recipients, only: [:show]
  
  # API endpoints for AJAX
  namespace :api do
    namespace :v1 do
      get 'senders/search', to: 'senders#search'
      get 'senders/:sender_id/recipients', to: 'recipients#index'
      get 'recipients/:recipient_id/bank_accounts', to: 'recipient_bank_accounts#index'
    end
  end
  
  # Friendly receipt URL
  get '/receipt/:id', to: 'transfers#receipt', as: :receipt
end

**Generate Controllers:**
```bash
rails g controller Transfers new create show index receipt
rails g controller Senders index show
rails g controller Recipients show
rails g controller Api::V1::Senders search
rails g controller Api::V1::Recipients index
rails g controller Api::V1::RecipientBankAccounts index
```

**Tasks:**
- [ ] Define routes in `config/routes.rb`
- [ ] Generate all controllers
- [ ] Implement TransfersController:
  - `new`: Empty form (no preloading needed)
  - `create`: Create sender/recipient/bank_account/transfer (nested creates)
    - Use transactions for atomicity
    - Handle all 3 scenarios (existing/new combinations)
  - `index`: List transfers with pagination (includes associations)
  - `show`: Display transfer details
  - `receipt`: Render receipt with special layout
- [ ] Implement API controllers:
  - `Api::V1::SendersController#search`: 
    - Search by name or phone
    - Return JSON with sender info + recipients count
  - `Api::V1::RecipientsController#index`: 
    - Return JSON of recipients for a sender
    - Include bank_accounts count
  - `Api::V1::RecipientBankAccountsController#index`: 
    - Return JSON of bank accounts for recipient
    - Mark which is default
- [ ] Add `skip_before_action :verify_authenticity_token` to API controllers
- [ ] Test routes: `rails routes | grep transfer`


### Day 4: Transfer Form (Frontend)

**Tasks:**
- [ ] Create transfer form view (`app/views/transfers/new.html.erb`)
- [ ] Build form structure with 3 progressive sections:
  
  **SECTION 1: Sender Search/Create**
  - Search input for sender (name or phone)
  - Search button
  - Results area (if existing sender found, show info card)
  - "Create New Sender" form (if not found):
    - Full name, phone, email
    - ID type, ID number
    - Country, address, city, state
    - Notes
  
  **SECTION 2: Recipient Selection/Create (appears after sender)**
  - If sender HAS recipients:
    - Dropdown to select existing recipient
    - "Add New Recipient" option
  - If sender has NO recipients OR "Add New" selected:
    - New recipient form:
      - Full name, phone, email
      - ID type, ID number
      - Country, address, city
      - Relationship (family, friend, business)
      - Notes
  
  **SECTION 3: Bank Account Selection/Create (appears after recipient)**
  - If recipient HAS bank accounts:
    - Dropdown to select existing bank account
    - "Add New Bank Account" option
  - If recipient has NO accounts OR "Add New" selected:
    - New bank account form:
      - Bank name
      - Account holder name
      - Account number
      - Account type (savings, checking)
      - Bank code (optional)
  
  **SECTION 4: Transfer Details (always visible)**
  - Amount sent input
  - Exchange rate (TRM) input
  - Fee amount input (default $5)
  - Payment method selector (cash, debit_card, credit_card)
  - Delivery method selector (bank_transfer, cash_pickup)
  - Purpose input (optional)
  - Notes textarea (optional)

- [ ] Add calculation summary section:
  - Amount received (calculated)
  - Total charged (calculated)
  - Display in real-time
- [ ] Style with Tailwind CSS:
  - Progressive disclosure (show sections as user progresses)
  - Card-style containers for each section
  - Clear visual hierarchy
  - Step indicators (1→2→3→4)
  - Mobile-responsive
- [ ] Add form validation error display
- [ ] Test form submission with all scenarios:
  - Existing sender + existing recipient + existing account
  - Existing sender + new recipient + new account
  - New sender + new recipient + new account

### Day 5: Dynamic Form Behavior (Stimulus)

**Generate Stimulus Controller:**
```bash
rails g stimulus transfer-form
```

**Tasks:**
- [ ] Create Stimulus controller: `app/javascript/controllers/transfer_form_controller.js`
- [ ] Define targets:
  - senderSearchInput
  - senderResultsContainer
  - senderFormContainer (for creating new sender)
  - recipientSection
  - recipientSelect, recipientFormContainer
  - bankAccountSection
  - bankAccountSelect, bankAccountFormContainer
  - amountInput, rateInput, feeInput
  - amountReceivedDisplay, totalChargedDisplay

- [ ] Implement actions:
  
  **searchSender()**: 
  - AJAX call to `/api/v1/senders/search?q=query`
  - If found: Display sender info, show recipient section
  - If not found: Show "Create new sender" form
  
  **selectExistingSender()**:
  - Load sender's recipients
  - Show recipient section
  - If has recipients: populate dropdown
  - If no recipients: show "Create recipient" form
  
  **loadRecipients()**: 
  - AJAX call to `/api/v1/senders/:id/recipients`
  - Populate recipient dropdown
  - Add "Add New Recipient" option
  
  **selectRecipient()**:
  - If existing recipient: load bank accounts
  - If "Add New": show recipient form
  
  **loadBankAccounts()**: 
  - AJAX call to `/api/v1/recipients/:id/bank_accounts`
  - Populate bank account dropdown
  - Auto-select default if exists
  - Add "Add New Bank Account" option
  
  **toggleRecipientForm()**:
  - Show/hide recipient creation form
  
  **toggleBankAccountForm()**:
  - Show/hide bank account creation form
  
  **calculateTotal()**: 
  - Real-time math calculations
  - amount_received = amount_sent × exchange_rate
  - total_charged = amount_sent + fee

- [ ] Progressive form display:
  - Section 1 always visible
  - Section 2 appears after sender is selected/created
  - Section 3 appears after recipient is selected/created
  - Section 4 always visible at bottom

- [ ] Add loading states/spinners during AJAX calls
- [ ] Handle errors gracefully (show error messages)
- [ ] Format currency displays (use Intl.NumberFormat)
- [ ] Add debouncing to search input (wait 300ms after typing)

- [ ] Test all interaction flows:
  - Search existing sender → select → shows recipients
  - Search non-existent sender → shows create form
  - Select existing recipient → shows bank accounts
  - Select "Add New Recipient" → shows recipient form
  - Select existing bank account → enables submit
  - Select "Add New Bank Account" → shows account form
  - Change amounts → totals update immediately
  - Form validation works for all scenarios

### Day 6: Receipt Page

**Tasks:**
- [ ] Create receipt layout: `app/views/layouts/receipt.html.erb`
  - Minimal header/footer
  - Clean, print-friendly design
  - No navigation menu
- [ ] Create receipt view: `app/views/transfers/receipt.html.erb`
- [ ] Design receipt card:
  - Professional header with transfer number
  - Date and time
  - Sender information section
  - Recipient information section
  - Bank account details (masked: ****5678)
  - Transfer details (amounts, rates, fees)
  - Status indicator (completed, green checkmark)
  - Footer with thank you message
- [ ] Style with Tailwind:
  - White card, centered
  - Clean typography
  - Proper spacing
  - Mobile-optimized for screenshots
- [ ] Add action buttons:
  - "Share via WhatsApp" button
  - "Download as Image" button
- [ ] Include html2canvas library:
  - Add to importmap: `pin "html2canvas", to: "https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"`
- [ ] Implement WhatsApp share function:
  - Use `wa.me` URL with pre-filled message
  - Include transfer summary and receipt link
  - Open in new window
- [ ] Implement download image function:
  - Use html2canvas to capture receipt div
  - Convert to PNG
  - Trigger download with filename: `receipt-TRF-2025-0001.png`
- [ ] Test on mobile device:
  - Receipt displays correctly
  - WhatsApp button works
  - Screenshot quality is good
  - Download works on iOS/Android

### Day 7: Transfer List & Navigation

**Tasks:**
- [ ] Create transfer index view: `app/views/transfers/index.html.erb`
- [ ] Build table with columns:
  - Transfer Number
  - Date
  - Sender Name
  - Recipient Name
  - Amount Sent
  - Amount Received
  - Status
  - Actions (View Receipt)
- [ ] Add search form:
  - Search by transfer number
  - Search by sender name
  - Date range filter
- [ ] Implement search logic in controller
- [ ] Add pagination (kaminari or pagy gem):
  - 25 transfers per page
  - Page navigation at bottom
- [ ] Style table with Tailwind:
  - Responsive (stack on mobile)
  - Hover effects
  - Status badges (green for completed)
- [ ] Create navigation menu:
  - Logo/brand name
  - "New Transfer" link
  - "Transfer History" link
  - "Senders" link (future)
  - User info / logout (future)
- [ ] Add navigation to application layout
- [ ] Improve flash messages styling
- [ ] Add loading states
- [ ] Test all flows end-to-end

### Day 8: Testing & Deploy

**Tasks:**
- [ ] Manual testing checklist:
  - Create new sender flow
  - Create new recipient with bank account
  - Create transfer (all fields)
  - View receipt
  - Share via WhatsApp (test on mobile)
  - Download receipt image
  - Search transfers
  - Filter by date
  - Pagination works
- [ ] Test edge cases:
  - Form validation errors display correctly
  - Can't submit with missing required fields
  - Recipient must belong to selected sender
  - Bank account must belong to selected recipient
  - Invalid amounts rejected
  - Transfer number generates uniquely
- [ ] Fix any bugs found
- [ ] Update seed data with realistic examples
- [ ] Prepare for deployment:
  - Create Render.com account
  - Create new Web Service
  - Connect GitHub repository
  - Add environment variables (DATABASE_URL, RAILS_MASTER_KEY)
  - Set build command: `bundle install && rails assets:precompile`
  - Set start command: `rails server`
- [ ] Deploy to production
- [ ] Create PostgreSQL database on Render
- [ ] Run migrations: `rails db:migrate RAILS_ENV=production`
- [ ] Test production environment thoroughly
- [ ] Setup automated backups on Render
- [ ] Document deployment process

---

## Phase 2: Enhancements (Week 2)

### Sender Management
- [ ] Create senders index page with search
- [ ] Add "New Sender" form with full fields
- [ ] Add "View Sender" detail page
  - Show sender info
  - List all recipients
  - Show transfer history
  - Total sent statistics
- [ ] Add "Edit Sender" functionality
- [ ] Validate phone number format (E.164)
- [ ] Add sender notes field

### Recipient Management
- [ ] Create recipients index page (global view)
- [ ] Add "New Recipient" standalone form
- [ ] Enhance "View Recipient" page:
  - Recipient details
  - Bank accounts list
  - Transfer history
  - Edit button
- [ ] Add "Edit Recipient" form
- [ ] Add "Add Bank Account" form
- [ ] Mark/unmark favorite recipients
- [ ] Validate uniqueness within sender scope

### Bank Account Management
- [ ] List all bank accounts for a recipient
- [ ] Add new bank account form
- [ ] Edit bank account details
- [ ] Set/change default account
- [ ] Deactivate accounts (soft delete)
- [ ] Show account usage history (which transfers used it)

### Reporting
- [ ] Create reports page
- [ ] Monthly summary dashboard:
  - Total transfers count
  - Total amount sent (USD)
  - Total amount received (COP)
  - Average exchange rate
  - Total fees collected
- [ ] Top senders report (by transaction count and amount)
- [ ] Top recipients report
- [ ] Transfer by delivery method chart
- [ ] Export to CSV functionality
- [ ] Date range selector
- [ ] Print-friendly report layout

### Search & Filters
- [ ] Advanced search page
- [ ] Filter transfers by:
  - Sender
  - Recipient
  - Status (completed, pending, cancelled)
  - Date range
  - Amount range
  - Delivery method
  - Payment method
- [ ] Sort columns (amount, date, sender, recipient)
- [ ] Save frequently used filters
- [ ] Quick filters (Today, This Week, This Month)

### UI Improvements
- [ ] Add dashboard/home page:
  - Recent transfers (last 10)
  - Today's statistics
  - Quick actions (New Transfer button)
  - Pending transfers count
- [ ] Improve mobile responsive design
- [ ] Add keyboard shortcuts:
  - `Ctrl/Cmd + N` for New Transfer
  - `Ctrl/Cmd + F` for Search
  - `Esc` to close modals
- [ ] Add tooltips for form fields
- [ ] Improve loading states (skeleton screens)
- [ ] Add confirmation dialogs for destructive actions
- [ ] Breadcrumbs navigation
- [ ] Better error pages (404, 500)

---

## Phase 3: Automation (Month 2)

### Authentication
- [ ] Install Devise gem
- [ ] Generate User authentication
- [ ] Add login/logout functionality
- [ ] Protect all routes (except receipt page)
- [ ] Add "Remember me" functionality
- [ ] Password reset flow
- [ ] Update seed to create default admin user

### WhatsApp API Integration
- [ ] Sign up for Twilio account
- [ ] Get WhatsApp Business API access
- [ ] Install `twilio-ruby` gem
- [ ] Create WhatsApp service class: `app/services/whatsapp_service.rb`
- [ ] Add "Send Receipt" button to transfer show page
- [ ] Implement automated receipt sending:
  - Send message with transfer summary
  - Include receipt URL
  - Handle delivery status
- [ ] Add delivery status tracking in database
- [ ] Show delivery status in transfer list
- [ ] Handle failures gracefully (retry logic)
- [ ] Add manual resend option

### Notifications
- [ ] Send SMS confirmation to sender (optional)
- [ ] Send SMS notification to recipient
- [ ] Email receipt as backup (ActionMailer)
- [ ] Add notification preferences per sender/recipient
- [ ] Notification templates management
- [ ] Track notification delivery status

### Background Jobs
- [ ] Configure Solid Queue (built-in Rails 8)
- [ ] Move receipt generation to background job
- [ ] Move WhatsApp sending to background job
- [ ] Move email sending to background job
- [ ] Add job status indicators in UI
- [ ] Add job retry logic
- [ ] Monitor failed jobs
- [ ] Job history page (admin only)

---

## Phase 4: Advanced Features (Month 3+)

### Role-Based Permissions
- [ ] Add roles to users (admin, operator, viewer)
- [ ] Install Pundit gem for authorization
- [ ] Create policies:
  - Admin: full access
  - Operator: create/view transfers
  - Viewer: read-only access
- [ ] Protect actions based on roles
- [ ] Add role selector in user management

### Audit Trail
- [ ] Install PaperTrail gem
- [ ] Track changes to:
  - Transfers (status changes)
  - Senders (info updates)
  - Recipients (info updates)
  - Bank accounts (changes)
- [ ] Show change history in detail pages
- [ ] "Who changed what when" report
- [ ] Revert changes functionality (admin only)

### Customer Portal (Future)
- [ ] Create separate authentication for customers
- [ ] Customer login page
- [ ] Customer dashboard:
  - View their transfer history
  - Download receipts
  - Update their bank accounts
  - Contact support
- [ ] Public API for mobile app integration

### Analytics & Charts
- [ ] Install Chart.js or Chartkick
- [ ] Transfers over time graph (line chart)
- [ ] Revenue by month (bar chart)
- [ ] Exchange rate trends
- [ ] Top countries/banks chart
- [ ] Customer growth chart
- [ ] Delivery method distribution (pie chart)

### Additional Features
- [ ] Multi-currency support (EUR, GBP)
- [ ] Real-time exchange rate API integration (CurrencyLayer, Fixer.io)
- [ ] Receipt customization (logo upload, colors)
- [ ] Email templates designer
- [ ] SMS templates designer
- [ ] Bulk transfer import (CSV upload)
- [ ] Integration with accounting software (QuickBooks API)
- [ ] Compliance features (KYC/AML flags)
- [ ] Transaction limits and alerts
- [ ] Fraud detection rules

---

## Ongoing Tasks

### Maintenance
- [ ] Setup automated daily database backups
- [ ] Monitor error logs (install Rollbar or Sentry)
- [ ] Weekly security updates check
- [ ] Monthly dependency updates (`bundle update`)
- [ ] Performance monitoring (install Skylight or Scout APM)
- [ ] Database query optimization (check N+1 queries)
- [ ] Disk space monitoring
- [ ] SSL certificate renewal (automatic with Render)

### Documentation
- [ ] Keep README updated with new features
- [ ] Document API endpoints (if created)
- [ ] Create user guide (PDF/web page)
- [ ] Create operator training materials
- [ ] Document deployment process
- [ ] Document backup/restore procedures
- [ ] Create troubleshooting guide

### Performance Optimization
- [ ] Add database indexes where needed
- [ ] Optimize slow queries (use `explain analyze`)
- [ ] Add caching for frequently accessed data
- [ ] Implement CDN for assets (Cloudflare)
- [ ] Database connection pooling
- [ ] Background job queue optimization

---

## Bugs / Issues
(Track in GitHub Issues instead)

---

## Ideas / Future Considerations
- Multi-language support (English/Spanish toggle)
- Mobile app (React Native or Flutter)
- Franchise/multi-location support
- Customer referral program
- Loyalty points system
- Integration with local banks for automated transfers
- Cryptocurrency support
- Blockchain-based transfer verification
- AI-powered fraud detection
- Voice-activated transfer creation (Alexa/Google Home)