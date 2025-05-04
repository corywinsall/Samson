// App.jsx import { useState } from "react"; import { ShoppingCart } from "lucide-react"; import { loadStripe } from "@stripe/stripe-js";

const stripePromise = loadStripe("pk_test_your_publishable_key");

const popularProducts = [ { id: 1, name: "Apple AirPods Pro", price: 249.99, image: "https://store.storeimages.cdn-apple.com/4668/as-images.apple.com/is/MWP22?wid=532&hei=582&fmt=png-alpha&.v=1591634795000", }, { id: 2, name: "Samsung Galaxy Watch 6", price: 299.99, image: "https://images.samsung.com/is/image/samsung/p6pim/levant/2307/gallery/levant-galaxy-watch6-sm-r930nzeamea-thumb-537042700?", }, { id: 3, name: "Nintendo Switch", price: 299.0, image: "https://www.nintendo.com/content/dam/noa/en_US/hardware/switch/nintendo-switch-package.jpg", }, { id: 4, name: "Dyson V11 Vacuum Cleaner", price: 599.99, image: "https://dyson-h.assetsadobe.com/is/image/content/dam/dyson/images/products/primary/268804-01.png", }, { id: 5, name: "Oculus Quest 2", price: 399.0, image: "https://upload.wikimedia.org/wikipedia/commons/6/6e/Oculus_Quest_2_Headset.jpg", }, ];

export default function App() { const [cart, setCart] = useState([]);

const addToCart = (product) => { setCart((prev) => [...prev, product]); };

const checkout = async () => { const stripe = await stripePromise; const response = await fetch("http://localhost:4242/create-checkout-session", { method: "POST", headers: { "Content-Type": "application/json" }, body: JSON.stringify({ items: cart.map((item) => ({ name: item.name, price: item.price })), }), }); const data = await response.json(); stripe.redirectToCheckout({ sessionId: data.id }); };

return ( <div className="p-6 bg-gray-100 min-h-screen"> <header className="flex justify-between items-center mb-6"> <h1 className="text-2xl font-bold">Top Trending Products</h1> <div className="flex items-center space-x-2"> <ShoppingCart /> <span>{cart.length}</span> </div> </header>

<div className="grid grid-cols-1 sm:grid-cols-3 gap-6">
    {popularProducts.map((product) => (
      <div key={product.id} className="bg-white p-4 rounded-xl shadow">
        <img
          src={product.image}
          alt={product.name}
          className="w-full h-48 object-contain rounded"
        />
        <h2 className="mt-2 text-lg font-semibold">{product.name}</h2>
        <p className="text-gray-600">${product.price.toFixed(2)}</p>
        <button
          onClick={() => addToCart(product)}
          className="mt-2 w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700"
        >
          Add to Cart
        </button>
      </div>
    ))}
  </div>

  {cart.length > 0 && (
    <div className="mt-8 bg-white p-4 rounded-xl shadow">
      <h3 className="text-xl font-bold mb-2">Cart Summary</h3>
      <ul className="mb-4">
        {cart.map((item, index) => (
          <li key={index} className="text-gray-700">
            {item.name} - ${item.price.toFixed(2)}
          </li>
        ))}
      </ul>
      <button
        onClick={checkout}
        className="bg-green-600 text-white py-2 px-4 rounded hover:bg-green-700"
      >
        Checkout with Stripe
      </button>
    </div>
  )}
</div>

); }

# Samson