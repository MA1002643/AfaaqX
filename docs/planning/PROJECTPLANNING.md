## Website Behavior

#### Login

When users access the web application, they can log in or register (or simply close this window to navigate around the app). Business owners must register using an email address ending in @ad.amc.pk to access the dashboard (administrators will have full access). Whether you log in as an admin, staff, or user, you should receive a second authentication prompt via Microsoft Authenticator or a code sent to your email or phone.

#### Access Control

I want the web app to be managed with access control. If the user uses @ad.amc.uk as the username, the admin dashboard should be visible (with full control). If a user account is created with @amc.pk for regular staff, they should be able to perform only the tasks mentioned above. If a customer logs in or registers, the dashboard should not be visible.

> - For the dashboard section, the menu should be placed on the left and the main dashboard area on the right.
> - When a notification is sent due to user sections described below, it should be sent to all staff/admins who are logged in.
> - Notification should be sent once the fine is paid, etc. Notification should be sent to the user that requested a service for instance the fine to be paid.
> - Tests case should be writing clearly to check the entire functionality of the website also to check the encryption and safety side for the admin, staff and users.
>   > Dissertation titles should be decided before submitting the dissertation. Check if the unit leader or form needs to be amended for this change.
> - Ensure that my entire project adheres to best web development practices (List them for me).
> - Ensure the website's layout is clean and professional throughout.
> - Ensure the website is responsive
> - The website should include an ENG/PKR translation option on both user interface and dashboard.
> - The entire website should also have dark/light color theme functionality
> - what programming languages should I use for this project both in the frontend and backend (including frameworks).

#### Register Staff Members - Admin

Admin users will have extra functionality. In the dashboard menu bar, there will be an additional button labelled "staff". The staff page will list all staff members, and the admin should be able to add, edit, and delete them. To create a staff member account, the form should include only Name, Surname, Staff ID, Date of Birth, and an email field. Once a user (Staff) is created, they should receive an email to create a new password and register for Microsoft authentication. If the user already exists and you click Edit, a password reset option should appear. If clicked, it should send an email to the staff member's email address stored in the database to reset the password. This should include a free-tier database with a table to store all form data. with the following fields: Name, Surname, Staff ID, Date of Birth, and an email field.

#### Add products to the website - Admin

If logged in as an admin, the user will also have extra functionality in the dashboard menu bar: an additional button labelled “Products”. When you click this button, the Products at the top should include a categories section: All, Mobile, Phone Cover, Headphones, Accessories, and Others. Each category should list its items, and all categories should list all items from all other categories. Each item in each category should be displayed as a card, and each card should include images (Pagination). Under the images, these should clearly be priced in PKR rupees, and there should be a button at the end of each card saying "add to cart". At the end of all the items listed, there should be a card with a plus sign saying add item, When clicking on that, a form would be opened, which will have a heading saying “List a new product” with input fields saying Product title, Quantity could have a scroll numeric table from 1-100, Price filed (The currency on this field should always be set to PKR Rupees), Description input field, option to select the category of this item and finally attach image (The images option should be to add up to 5 images). At the end of this form, there should be two buttons: one labelled “Preview” and the other “Publish item”. If Preview is pressed, the admin should be able to view the product saved as a draft in the selected category. The item will not be visible to the users until the publish button is pressed. For all listed items, the admin can amend or delete them. This should include a free-tier database with a table to store all form data.

#### Staff

If you logged in as a staff user, the “Product” and “staff” buttons should be disabled in the dashboard menu list.

#### Register a new user - Staff/admin

The staff/admin should have a menu button labelled “Users” that lists registered users. For each registered user, list their name. On each list, there should be two buttons: delete and amend. Use an X icon to delete user data and a pen icon to amend it. If the delete button is pressed, a pop-up should be displayed asking, "Are you sure you want to delete the 'name of the user' record?" And two buttons saying “Yes”,” No”. If yes is pressed, the button should be deleted; if no is pressed, nothing should happen. At the top of this table, add a search option to find the user by name, date of birth, number, or email address. Next to the Search option, there should be a button labelled "Add user". Once the button is pressed, the form should appear labelled Create a new user. In that form, there should be input fields: Name, Surname, Date of Birth, Contact number, and Email address. To the right of the form, there should be a button labelled "Save." When pressed, the user should be added to the bottom of the user list. The user lists should also include pagination. (If users register themselves, the users list should also update.) This should include a free-tier database with a table to store all form data.

#### Accept online orders - Staff/admin

When a user or anyone else places an online order for collection, anyone logged in as a staff member or admin should see a pop-up that includes “Name of the customer”, “Quantity”, “Name of Item”, and “Collection Time”. Once you click on it, A form should appear with all these details. At the end of the form, it should say you have x more of these left in stock. Under that, it should include two buttons: Accept and Decline. Either way, the user who placed the order should receive a text message stating whether their “order number”, “Item name”, and “Quantity” have been accepted or rejected. Each order notification should be displayed in a stack of forms in the top-right corner of the page. This should include a free-tier database with a table to store all form data.

On the dashboard menu bar, for both staff and administrators. There should be an “Orders” button to access the orders page. That page should have a table displaying all orders, starting with the most recent (Pagination should also be applied to this). Each name in a list should also be in a “Name, Surname”, “Item name” and “Quantity” Accepted or Rejected status. At the top of the table, include a search bar that allows users to search for orders by name, surname, date, order number, phone number, and email address. Next to the search bar, add a filter option. Once the admin or staff member clicks it, a dropdown list should open: Accepted, Rejected, Received, and Coming up. If any of them is selected, the list should be filtered accordingly. This should include a free-tier database with a table to store all form data.

#### Appointment bookings dashboard - Staff/admin

On the dashboard menu bar, for both staff and administrators. There should be a button called “appointments” under this button; there should be a page which should have a calendar interface in a calendar layout on the top of the calendar, there should be a right and left button by pressing the right button should display the next month is displayed and pressing the left button should display the previous month is displayed. to see each date booked appointments click on the date and a scrollable list from 00:00 to 00:00 will come up like in google calender, to show that specific date appointments bookings. This should include a free-tier database with a table to store all calendar data. If the user schedules an appointment to seek advice or support with a CV that should automatically update the calendar for other users, admins, and staff, as well as the database as soon as the appointment is booked, the admin or the staff, whoever is logged in, should receive a pop-up notification in a top right side of the screen as banner like in the macos IOS once the user click on that banner it should open up the full form filled by the user with all the fields disabled as they will be already filled by the user. according to the availability and the nature of the appointment at the end of the form the staff/admin side should have two buttons "Accept", "Decline" either way the user should be notified if their appointment request is accepted or rejected via their contact details. Until the appointment is not accepted or rejected to other users or staff/admin the time requested should be grayed out to ensure appointments are not booked on top of each other. If the user/staff want to book an appointment from their end they simply click on the preferred date, once the scrollable list appear the staff/admin can click on any available slots once staff/admin clicks on the slot a form should appear to be filled with customer fist name, last name, phone number, email address, appointment with (Name of the staff, admin), reason for appointment (a dropdown list), duration (dropdown list 15,30,45 mins or an hour) and at the end of the form there should be a button saying "confirm" once confirm is pressed the calender should update. reflecting the appointment booked. either way if the user or staff/admin has booked an appointment on the user receives a message saying that your appointment has been booked, it should also include a link to make a payment for the service with the amount auto selected and disabled once the user makes a payment the admin/staff should receive a notification on top right of the page. Staying appointment number: @@@ had payment received. for any booked appointments payment pending should be set blue color and once paid it should be set to turn to Green color.

#### Set monthly mobile phone top-ups - Staff/admin

on the dashboard menu there should be a button called "top-ups" under that button there should be a page, on that page there should be a table with a list of all the users that have requested monthly top-ups themselves and list of those users that the admin/staff have set up the weekly/monthly top-ups for them. each user will be displayed with a name+surname of the user. with a pagination applied to the table. The table should display each user in a list view. on the top right of the table there should be a search bar each user can be searched my name, surname, date of birth and phone number and phone number operator. Next to the search bar each there should be a filter option with a dropdown list (Coming up, recently paid off, Monthly, Weekly) if any of the filter is selected the data should be filtered accordingly. Each user listed in this table should be clickable with option to edit details, such as their number, re-occurrence of the top-up (e.g weekly, monthly), name and surname of the customer with their contact details. According to this table regardless if the user it self set up a top-up or staff or admin does it for a customer on daily basis at 9:00 am pakistani time the admin or staff that is logged in should receive a banner notification on the top left side of the screen notifying them to do to top-up for the user. the banner should be clickable once it is click it should give two option to staff/admin Direct Debit or money to received and top-up completed in either way the customer should receive a text reminder/message once one of the two option is selected. There should a free tier database in the back to store all the information from the table and the database should update automatically.

#### Payment Services - Staff/admin

On the dashboard menu there should be a button called "Payment Services" under that button they should be a page, on that page there should be a table with a list of all the users that have requested different payment services both requested by the users by filling in the form and also those that have been inputted by the staff/admin. Each user should be displayed in the table with their name and surname displayed, in a list the table should have pagination applied to it. on the top right of the table there should be a search bar each user can be searched by their name, surname, date of birth, type of service they requested. Next to the search bar each there should be a filter option with a dropdown list of different payment services the filtering should also have option for paid and still to be paid, pending and rejected. If any of the filtering is applied, it should filter the table accordingly. Each user listed in this table should be clickable, once the staff/admin click on the user listed in this table the a pre-filled form by the user should come up with user name, surname, date of birth, ID number, contact number, PSID no. and category (Disabled when the form is already filled in by the user). next to the filter option there should be a button called "New Service Request" that should displayed if logged in as staff/admin if clicked that should bring up a form including the following fields such as "name, surname, date of birth, ID number, contact number, PSID no. and category (List of different services the user want) at the end of the form there should be a button called "Create Service", if that button is clicked, that should be saved in the table by updating the database and the table too. for any displayed user already on the table, form should open up with auto filled information where the staff/admin will have a filled called attached "Receipt" in here the staff/admin will attach the picture of the receipt of the service requested. another field in that form of the only visible to staff/admin called "status" with a drop down list "not processed, in process, completed" each time the status is changed the customer should be sent an update massaged saying you ticket is in process or your ticket is completed once the status is changed to completed the staff/admin should get an option to close the ticket. the ticket should only be close if the status is changed to "Completed" and a receipt has been attached to the ticket. one the ticket is closed it should disappear from the main table and should be added to a filter table called "paid"

#### Live Chat - Staff/admin

This website should also have a live chat function, so once the customer clicks on it fills in their name/surname email address, drop down list of reason to contact and message field and a send button once they send the message if is during the working our there should be also a live function like button displayed on the bottom right of the screen so once the user staff/admin clicks on it shows the customer first and second name once the staff/admin clicks on it they should be able to reply to the user similar to whatsapp interface. very time user or staff/admin reply or sends a message they should be a notification sound. if it is out of working ours there should be AI implementation to answer users questions to best the abilities until the staff returns next working the they will still be able to see what the user has asked in their query and what AI has told them and they should be able to reply to the query.

#### Login/Register - user

Once the user visits the website, they can navigate the website without Logging in or Register but when it comes to booking a service or ordering a product, book a service or session and for the live chat.

#### User interface

for the user side of the website there should 5 different pages called:

- Home page (Homepage should be very modern and excellent)
- About page (should have a list of the staff and a bit about the business and other things)
- Products page (Already explained)
- Services page (Already explained)
- Contact us page (the contact us page should list the below Business contact number, Business email address, Business opening hours, Software specialist (Qasim Shahzad), Hardware specialist (Asif Shahzad))

#### Products page - user

On the products side of the page, the layout of the page should be modern and to a hight standard, on top saying welcome to AMC store under that there should be a list of all different categories, once the user clicks on any of the category a list of all the products should come up for both currently available or those currently out of stock. so the user can click on any of the item to view full description of the product also a review of customers that have already bought the product the user will have a choice to select the quality of item and the add to chart button, if pressed that should add the product to the chart. each page should have pagination once the admin adds the products to a specific category it should automatically update the list of items in each category the new product/item is being added to.

#### chart - user

Where these should be a chart to handle the users purchasing items and handle the entire thing with a free tier database and payment handling too.

#### service page - user

In the service page there should be a form where the use can book/request the following services:

- Book an advice session (CV, education)
- Book a computer fix (hand in slot, pick up slot)
- Pay for phone top-ups (direct debit, card payment, PayPal)
- Token
- Passport fees
- ID card renewal
- Driving license renewal
- Scanning
- Printing
- Email services
- Top-up
- CV specialist
- Car fine payment
- Car service available (Different online things about cars)
- Education services available (Admissions, school letters, etc.)
- Computer services available (Laptop Windows, software, hardware)

#### APIs for this website

- Calendar API
- Map API
- Live chat API
- Recurring sessions that are booked in the calendar.

#### General info about the project

- (Product page) All purchases made online from the products page will be stored. It will be a click-and-collect service, so customer information will be stored, including the date and time of their pickup.
- (Top up mobile phones - service page) For top-up mobile phones, information on which phone was topped up, along with customer information, will be saved. (This service will also have an option to auto top-up phone on a monthly basis, and possibly will have an option to set up a monthly direct debit).
- (CV specialist - services page) CV specialist: This will again be a form with a backend to store information about customers who contact the business with their queries.
- (Car fine payments - services page) Car fine payments: This will again record all car fine payments processed by the business. Again, this will be recorded in the database for each customer.
- (Education services - services page) Education services will also have a database that saves information about different admissions filed and school letters created for different customers.
- (Computer services - services page) Computer services will also have all their information saved in a database. (An auto follow-up appointment will be arranged with the customer who will have a new laptop window installed or a software installed on their machine to double-check that everything is working as it should be.)
- (Overall website features) The live chat will also store all chat logs in a database.
- (Contact us Page) The contact us page will also store a database, so any customer contacting the business will have their details saved. On this page, a map API will be used.
- (Admin portal page) Overall, the website's admin portal will use the Calendar API.
