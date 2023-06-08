<html>
<head>
  <title>Payment Gateway HTML Page</title>
  <script src="https://js.stripe.com/v3/"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f7f7f7;
    }

    h1 {
      color: #333;
      text-align: center;
      margin-top: 40px;
    }

    .container {
      max-width: 400px;
      margin: 0 auto;
      padding: 20px;
      background-color: #fff;
      border-radius: 5px;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    }

    label {
      display: block;
      margin-bottom: 10px;
      color: #555;
    }

    input[type="text"],
    input[type="number"] {
      width: 100%;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    #card-element {
      border: 1px solid #ccc;
      padding: 10px;
      border-radius: 4px;
    }

    button {
      display: block;
      width: 100%;
      padding: 10px;
      margin-top: 20px;
      color: #fff;
      background-color: #333;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    button:disabled {
      background-color: #ccc;
      cursor: not-allowed;
    }

    #card-errors {
      color: #dc3545;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <h1>Proceed with Payment</h1>
  <div class="container">
    <form id="payment-form">
      <div>
        <label for="card-element">
          Credit or debit card
        </label>
        <div id="card-element">
          <!-- Stripe Card Element will be inserted here. -->
        </div>
      </div>
      <div id="card-errors"></div>
      <button id="submit-button" disabled>Pay Now</button>
    </form>
  </div>

  <script>
    // Create a Stripe client
    var stripe = Stripe('YOUR_STRIPE_PUBLISHABLE_KEY');

    // Create an instance of Elements
    var elements = stripe.elements();

    // Create a Card Element
    var card = elements.create('card');

    // Mount the Card Element to the card-element div
    card.mount('#card-element');

    // Handle form submission
    var form = document.getElementById('payment-form');
    form.addEventListener('submit', function(event) {
      event.preventDefault();

      // Disable the submit button to prevent multiple clicks
      document.getElementById('submit-button').disabled = true;

      stripe.createToken(card).then(function(result) {
        if (result.error) {
          // Inform the user if there was an error
          var errorElement = document.getElementById('card-errors');
          errorElement.textContent = result.error.message;

          // Enable the submit button
          document.getElementById('submit-button').disabled = false;
        } else {
          // Send the token to your server
          // You can make an AJAX request here to process the payment on your server

          // Example AJAX request
          var xhr = new XMLHttpRequest();
          xhr.open('POST', '/charge', true);
          xhr.setRequestHeader('Content-Type', 'application/json');
          xhr.onload = function() {
            if (xhr.status === 200) {
              // Payment successful, show a success message to the user
              alert('Payment successful!');
            } else {
              // Payment failed, show an error message to the user
              alert('Payment failed. Please try again.');
            }

            // Enable the submit button
            document.getElementById('submit-button').disabled = false;
          };
          xhr.send(JSON.stringify({token: result.token.id}));
        }
      });
    });
  </script>
</body>
</html>
