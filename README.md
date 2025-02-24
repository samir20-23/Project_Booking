## Project Brief 15  
Coworking Space Booking and Management**

**Context:**  
In today's dynamic professional landscape, coworking spaces offer flexible solutions for freelancers, startups, and established companies. Your team of four will develop a web application using Laravel for the backend and VueJS for the frontend. The application will enable users to browse, book, and manage coworking space reservations across various cities. Additionally, the system will integrate Stripe for secure, online payment processing.

**Interfaces:**

*Home Page:*  
- **Display:** Show available coworking spaces as cards sorted by availability date or location.  
- **Details:** Each card displays the space’s name, image, location, a brief description, and either a “Book Now” button or a “Fully Booked” indicator if reservations are closed.  
- **Search and Filters:**  
  - A search bar to filter spaces by name or city.  
  - Two filters to refine the list: one for availability between two dates and another for space type (e.g., open space, meeting room, private office).

*Space Details Page:*  
- **Information:** On clicking “Book Now” or “Fully Booked,” users are directed to a page that provides detailed information: name, a larger image, comprehensive description, amenities, pricing, and location.  
- **Booking Form:**  
  - Users can select a date, time slot, and number of participants.  
  - The form is accessible only to logged-in users.  
  - On clicking “Confirm Booking,” a confirmation message appears with options to view the invoice and reservation details.  
  - The booking is finalized only if the space is still available for the chosen time.  
- **Payment Integration:**  
  - Integrate Stripe to handle payments securely.  
  - The payment process is triggered after form submission and must be successfully completed before confirming the reservation.

*Authentication and Registration Page:*  
- **User Access:**  
  - New users must register to book a space.  
  - Existing users are required to log in before making a booking.

*User Profile:*  
- **Account Management:**  
  - Users can view and update their personal details (name, email, phone number, etc.).  
  - They can access their booking history in a table format displaying the reservation reference, booking date, amount paid, and options to view the invoice and booking details.

**Technologies:**  
- **Laravel:** Backend framework to manage business logic and server-side operations.  
- **VueJS:** Frontend framework to create a reactive and dynamic user interface.  
- **Repository Pattern + Service Layer:** For better separation of concerns, maintainability, and testability in code organization.  
- **Tailwind CSS:** For designing a clean, responsive, and modern user interface quickly and efficiently.

**Educational Modalities:**  
- Individual work  
- Group work (groups of four)

**Deliverables:**  
- A well-structured project folder  
- A complete and documented database  
- A link or file for project management (e.g., Git repository, project documentation)

**Skills Targeted:**  
- C2: Develop a static and responsive web interface  
- C3: Create a dynamic web interface  
- C6: Develop data access components  
- C7: Build the backend of a web or mobile web application

**Duration:**  
10 days
