**Bakery REST API**

Functional requirements
1. The website must display have two pages: Home and Menu
2. The website must display a homepage with bakery branding and a navigation menu,
3. The website must display navigation menu on the homepage containing links to Menu page, Gallery section on the homepage, Contact Us section on the homepage
4. The website must display gallery of images and expand and close the image on click
5. The website must display the bakery’s location on the embedded map
6. The website must display the bakery’s opening hours and service times
7. The website must display a menu page containing:
      - categorized menu items
      - each product must contain image, description and price
8. The website must allow to navigate back to homepage from menu page
9. The website must display links to social media on the bottom of the each page

Non-Functional requirements
1. The website should be available 24/7
2. The website must be visually consistent with chosen design
3. The website must load all pages within 3 seconds
4. The website must be responsive for different screen sizes
5. The website must be compatible with different web browsers
6. The website must use HTTPS, JWT authentication, input validation.

Entities
1. User
Represents a registered customer.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| user_id (PK) | LONG	| Unique user ID |
| role | VARCHAR(10)	| User role: admin/user |
| lastname | VARCHAR(100) | User last name |
| firstname |	VARCHAR(100) | User name |
| username | VARCHAR(100) | Unique email |
| password | VARCHAR(500) | Password |
| registration_date | DATE | Date user registered |

2. GalleryItem
Represents a image used in website.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| uimage_id (PK) | LONG	| Unique image ID |
| type | VARCHAR(50)	| Image type |
| image_url | VARCHAR(3000) | Image url |
| title |	VARCHAR(100) | Image title |

3. Product
Represents a dish as a menu item.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| product_id (PK) | LONG	| Unique product ID |
| gallery_item_id (FK) | LONG	| References GalleyItem |
| category | VARCHAR(50) | Product category |
| name | VARCHAR(100) | Product name |
| description |	VARCHAR(500) | Product description |
| price | DOUBLE | Product price |
| quantity | INT | Number of available products |
| created_on | DATE | Date added |

4. ContactInfo
Represents basic info about bakery.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| info_id (PK) | LONG	| Unique info ID |
| address | VARCHAR(500) | Physical bakey address |
| latitude | DECIMAL | Map latitude |
| longtitude | DECIMAL | Map longtitude |
| instagram_link | VARCHAR(500) | Link to instagram |
| pinterest_link | VARCHAR(500) | Link to Pinterest |
| x_link | VARCHAR(500) | Link to twitter |
| facebook_link | VARCHAR(500) | Link to Facebook |

5. OrderItem
Represents an item customer purchased.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| order_item_id (PK) | LONG	| Unique order item ID |
| product_id (FK) | LONG	| References Product |
| quantity | INT | Number of purchased products |

6. Order
Represents a customer purchase.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| order_id (PK) | LONG	| Unique order ID |
| purchase_time | LONG	| Purchase time |

7. Receipt
Represents a single purchase.

| Attribute	| Type | Description |
| --------- | ---- | ----------- |
| receipt_id (PK) | LONG	| Unique receipt ID |
| order_id (FK) | LONG	| References Order |
| order_item_id (FK) | LONG	| References OrderItem |
| customer_id (FK) | LONG	| References User |
| cashier_id (FK) | LONG	| References User |

**Operations supported by API**
- Registration and login
- Retrieve product list
- Filter products by category and price
- Retrieve gallery images
- Support pagination for all collections
- Support caching for read-only data
- Handle authentication for admin data

**REST API**
- Authentication: all protected endpoints require JWT token
- Registration
  - POST /api/users/register
      - registers a new user in the system, returns user information and JWT token
      - request headers: Content-Type: application/json
      - request body:
    ```json
	  {
		“role”: “Admin”,
		“lastname”: “Khomyn”,
		“firstname”: “Julia”,
		“username”: “juliakhomyn”,
		“password”: "password123",
		“email”: “julia@gmail.com”
    }
    ```
    - response headers: Content-Type: application/json
    - response body:
      - 201 Created
        ```json
	      {
		    “id”: “1”,
		    “role”: “Admin”,
		    “lastname”: “Khomyn”,
	      “firstname”: “Julia”,
	    	“username”: “juliakhomyn”,
		    “phone”: null,
		    “email”: “julia@gmail.com”,
		    “token”: “token”
      	 }
        ```
      - 400 Bad Request
        ```json
	      {
		    “error”: “Email julia@gmail.com already exists”
      	}
        ```
- Login
	- POST /api/users/login
	- authenticates user and returns JWT token
      - request headers: Content-Type: application/json
      - request body:
    ```json
	  {
		“email”: “julia@gmail.com”,
		“password”: “password123”
    }
    ```
    - response:
      - 200 OK
        headers:
        - Content-Type: application/json
        - Authorization: Bearer `<`jwt-token`>`
        ```json
	      {
	  	“id”: “1”,
	  	“role”: “Admin”,
	  	“lastname”: “Khomyn”,
	  	“firstname”: “Julia”,
		  “username”: “juliakhomyn”,
		  “phone”: null,
		  “email”: “julia@gmail.com”,
		  “token”: “token”
      	 }
        ```
      - 401 Unauthorized
        ```json
	      {
		    “error”: “Invalid username or password”
      	}
        ```
- Get all products
	- GET /api/products?page-1&limit=10
	- returns a paginated list of all products
	- response headers:
		Content-Type: application/json
		Cache-Control: public, max-age=300
	- response body:
	  200 OK
   ```json
	  {
		“page”: “1”,
		“limit”: “10”,
		“totalItems”: 25,
		“totalPages”: “3”,
		“products”: [
	  		{
				“id”: “1”,
				“category”: “Sweets”,
				“name”: “Churros",
				“description”: “Churros coated in sugar and cinnamon with chocolate sauce”,
				“price”: 30.00,
				“quantity”: 25,
				“imageURL”: “/images/menu/churros.png”
        }
		]
	  }
    ```
- Get single product
  - GET /api/products/{id}
  - returns product details by id
  - response headers:
    Content-Type: application/json
    Cache-Control: public, max-age=600
  - responses:
    - 200 OK
    - 404 Not Found
      ```json
	    {
		  “status”: “404”,
		  “error”: “Not Found",
		  “message”: “Product with id 1 not found.”,
		  “timestamp”: “2025-12-19T11:00:00Z“
  	  }
      ```

- Create product
  - POST /api/products
  - creates new product, requires admin role
  - request headers:
		Content-Type: application/json
		Authorization: Bearer <jwt-token>
	- request body:
    ```json
	  {
		“category”: “Croissant”,
		“name”: “Butter Croissant",
		“description”: “Classic French croissant with butter”,
		“price”: 30.00,
		“quantity”: 25,
		“imageURL”: “/images/menu/classicCroissant.png”
    }
    ```
	- response headers:
		Content-Type: application/json
	- response body:
    - 201 Created
      ```json
	    {
		  “id”: “2”,
		  “category”: “Croissant”,
	  	“name”: “Butter Croissant",
	  	“description”: “Classic French croissant with butter”,
	  	“price”: 30.00,
	  	“quantity”: 25,
	  	“imageURL”: “/images/menu/classicCroissant.png”
  	  }
      ```
    - 401 Unauthorized
      ```json
	    {
		  “status”: “401”,
		  “error”: “Unauthorized",
  		“message”: “User must be logged in to add product”,
  		“timestamp”: “2025-12-19T11:00:00Z“
      }
      ```
    - 403 Forbidden
      ```json
	    {
		  “status”: “403”,
		  “error”: “Forbidden",
		  “message”: “User does not have permission  to add product”,
		  “timestamp”: “2025-12-19T11:00:00Z“
  	  }
      ```
  - PUT /api/products/{id}
  - DELETE /api/products/{id}
