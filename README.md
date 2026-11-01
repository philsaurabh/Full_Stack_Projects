# Full Stack Projects

Web development projects covering the full stack: a **React–Redux** front end, **Node.js / Express** servers, **MongoDB** persistence, and **Stripe** payments.

| Project | Stack | What it shows | Folder |
|---|---|---|---|
| [React Book Seller](#1-react-book-seller) | React 16, Redux, React Router, React-Bootstrap | Single-page storefront with a Redux shopping cart | `React_Book_Seller-master/` |
| [Ebook Seller with Stripe](#2-ebook-seller-with-stripe) | Node.js, Express, Handlebars, Stripe API | Server-rendered checkout that charges a card through Stripe | `Ebook_seller_using_Stripe-master/` |
| [conFusion REST API (Coursera)](#3-confusion-rest-api--server-side-development-coursera) | Node.js, Express, MongoDB, Mongoose | Step-by-step build of a REST API with sessions and authentication | `Coursera-Backend-HK-Univ-Base-Code-main/` |

---

## 1. React Book Seller

A small online bookstore built with **React** and **Redux**. Users browse a catalogue of books, add or remove items, and review their cart on a separate page.

**Features**
- Product listing rendered from a JSON catalogue (`src/data/products.json`).
- Cart state in a Redux store, with actions `ADD`, `REMOVE`, `REMOVE_ALL` and a running total.
- Client-side routing with React Router: `/` for the catalogue, `/cart` for the cart.
- React-Bootstrap layout, with Redux DevTools support.

**Structure**
```
src/
├── App.js · Router.js            # app shell and routes
├── config/store.js               # Redux store
├── features/
│   ├── product-listing/          # product cards and add / remove buttons
│   └── cart/                     # cart view and reducer
├── pages/homepage.js · cartpage.js
└── data/products.json            # book catalogue
```

**Run**
```bash
cd React_Book_Seller-master
npm install
npm start                 # http://localhost:3000
```
Optionally, serve the catalogue as a mock REST API with `npm run jsonserver` (json-server on port 3004).

## 2. Ebook Seller with Stripe

A **Node.js / Express** app that sells a single ebook through the **Stripe** API. The landing page, rendered with Handlebars, opens Stripe Checkout. The server creates a Stripe customer from the returned token, charges **$25.00**, and renders a success page.

**Routes**
| Method | Path | Purpose |
|---|---|---|
| GET | `/` | Product page with the Stripe Checkout button |
| POST | `/charge` | Creates the customer and the charge |
| GET | `/success` | Confirmation page |

**Configuration.** Keys are never committed. In development, `config/keys.js` loads `config/keys_dev.js`, which is git-ignored, so create it yourself:

```js
// config/keys_dev.js
module.exports = {
  stripePublishableKey: 'pk_test_...',
  stripeSecretKey: 'sk_test_...'
};
```

In production (`NODE_ENV=production`) the keys come from the `STRIPE_PUBLISHABLE_KEY` and `STRIPE_SECRET_KEY` environment variables.

**Run**
```bash
cd Ebook_seller_using_Stripe-master
npm install
npm start                 # http://localhost:5000 (or $PORT)
```

**Test payment.** With test keys, use card `4242 4242 4242 4242`, any future expiry date, any CVC and any email address. Never enter real card details.

## 3. conFusion REST API — Server-side Development (Coursera)

Code written while following the Coursera course *Server-side Development with NodeJS, Express and MongoDB* (The Hong Kong University of Science and Technology). Each folder is one stage of the course, building up to a REST API for **conFusion**, a fictional restaurant.

| Folder | Stage | Key ideas |
|---|---|---|
| `node-http/` | Plain Node HTTP server | `http` module, serving static HTML |
| `node-express/` | Express server | Routing with `express.Router`, `morgan` logging, static files |
| `node-mongo/` | MongoDB driver | CRUD helpers in `operations.js`: insert, find, update, remove |
| `node-mongoose/` | Mongoose ODM | Schemas and models for dishes |
| `conFusionServer/` | Complete REST API | Express generator app, Mongoose models, cookies, sessions, authentication |

**conFusionServer API**

| Endpoint | GET | POST | PUT | DELETE |
|---|---|---|---|---|
| `/dishes` | list dishes | create a dish | — | delete all dishes |
| `/dishes/:dishId` | one dish | — | update the dish | delete the dish |
| `/dishes/:dishId/comments` | list comments | add a comment | — | delete all comments |
| `/dishes/:dishId/comments/:commentId` | one comment | — | update the comment | delete the comment |
| `/users/signup` | — | register a user | — | — |
| `/users/login` | — | log in (HTTP Basic auth header) | — | — |
| `/users/logout` | log out | — | — | — |

- **Models:** a `Dish` holds name, description, image, category, label, price (via `mongoose-currency`), a featured flag and embedded comments (rating, comment, author). A `User` holds a username, password and admin flag.
- **Authentication:** after login the server stores a session (`express-session` with `session-file-store`). An auth middleware blocks `/dishes` and static files for anyone who is not logged in.

**Run** (MongoDB must be running on `localhost:27017`)
```bash
mongod --dbpath=./mongodb/data            # from Coursera-Backend-HK-Univ-Base-Code-main/
cd conFusionServer
npm install
npm start                                 # http://localhost:3000
```

Example session with `curl`:
```bash
curl -X POST -H "Content-Type: application/json" \
     -d '{"username":"alice","password":"secret"}' localhost:3000/users/signup
curl -c cookies.txt -u alice:secret -X POST localhost:3000/users/login
curl -b cookies.txt localhost:3000/dishes
```

The earlier stages run the same way: `cd node-express && npm install && npm start`.

---

## Requirements

- **Node.js** and **npm**. These projects date from 2018–2020; Node 14 or 16 (for example via `nvm install 14`) avoids incompatibilities with their pinned dependencies. On newer Node, the React app may need `NODE_OPTIONS=--openssl-legacy-provider npm start`.
- **MongoDB** (Community Server) for the conFusion projects.
- A **Stripe** account with test-mode API keys for the ebook seller.

## Author

**Saurabh Sharma** · [philsaurabh.wordpress.com](http://www.philsaurabh.wordpress.com)

## License

`React_Book_Seller-master` includes its own permissive MIT-style `LICENSE` (© 2020 Saurabh Sharma). The conFusion code follows the Coursera course materials.
