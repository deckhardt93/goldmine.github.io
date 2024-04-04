<html lang="en-US">
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">

<!-- Begin Jekyll SEO tag v2.8.0 -->
<title>GoldMinePawnshop.github.io</title>
<meta name="generator" content="Jekyll v3.9.4" />
<meta property="og:title" content="goldmine.github.io" />
<meta property="og:locale" content="en_US" />
<link rel="canonical" href="https://deckhardt93.github.io/goldmine.github.io/" />
<meta property="og:url" content="https://deckhardt93.github.io/goldmine.github.io/" />
<meta property="og:site_name" content="Ottos.github.io" />
<meta property="og:type" content="website" />
<meta name="twitter:card" content="summary" />
<meta property="twitter:title" content="goldmine.github.io" />
<script type="application/ld+json">

<!-- End Jekyll SEO tag -->

    <link rel="stylesheet" href="/Ottos.github.io/assets/css/style.css?v=b1fe457a91e06eec11b892c00d174c1d1b69c888">
    <!-- start custom head snippets, customize with your own _includes/head-custom.html file -->

<!-- Setup Google Analytics -->


  </head>
  <body>
    <div class="container-lg px-3 my-5 markdown-body">
      
      <h1><a href="https://deckhardt93.github.io/goldmine.github.io/">goldmine.github.io</a></h1>
      

      <html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Shop menu</title>
  <script src="https://code.jquery.com/jquery-3.4.1.js" integrity="sha256-WpOohJOqMqqyKL9FccASB9O0KwACQJpFTUBLTYOVvVU=" crossorigin="anonymous"></script>
  <style>
    body, h2, form, label, p, button, select, input {
      font-size: 8;
      margin-right: 10px; /* Add a margin for spacing */
    }

    label {
		display: block;
		margin-bottom: 5px;
	}

    body, h2, form {
      text-align: center;
    }

    p {
      text-align: center;
      margin: 0; /* Remove default margin for <p> */
    }

    button {
      margin-top: 10px;
    }
	body, h2 {
	font-weight: bold;
	}
	body {
	background-color: grey;
	}
  </style>
  <script>
    function calculateTotals() {
  let total = 0;

  // Calculate total from selected items
  const menuItems = document.querySelectorAll('.menu-item:checked');
  menuItems.forEach(item => {
    const price = parseFloat(item.dataset.price);
    const quantity = parseInt(item.nextElementSibling.value);

    if (!isNaN(price) && !isNaN(quantity) && quantity > 0) {
      // Exclude "Mystery Box" from discounts
      if (item.classList.contains('exclude-discount')) {
        total += price * quantity;
      } else {
        total += price * quantity * (1 - ($("#discount").val() / 100));
      }
    }
  });

  // Change commission rate from 5% to 10%
  const commission = total * 0.20;

  document.getElementById('total').innerText = total.toFixed(2);
  document.getElementById('commission').innerText = commission.toFixed(2);
}

    function SubForm() {
  // Check if the total is not calculated
  const total = $("#total").text().trim();
  if (total === "") {
    alert("Please calculate the total first!");
    return;
  }

  // Check if the employee name is provided
  const employeeName = $("#employeeName").val();
  if (employeeName.trim() === "") {
    alert("Employee Name is required!");
    return;
  }

  // Get selected menu items and quantities
  const orderedItems = [];
  const menuItems = document.querySelectorAll('.menu-item:checked');
  menuItems.forEach(item => {
    const itemName = item.parentNode.textContent.trim();
    const price = parseFloat(item.dataset.price);
    const quantity = parseInt(item.nextElementSibling.value);

    if (!isNaN(price) && !isNaN(quantity) && quantity > 0) {
      orderedItems.push({
        name: itemName,
        price: price,
        quantity: quantity
      });
    }
  });

  // Calculate total and commission
  const totalValue = parseFloat($("#total").text());
  const commission = parseFloat($("#commission").text());
  const discount = parseFloat($("#discount").val());

  // Prepare data for API submission
  const formData = {
    "Employee Name": employeeName,
    "Total": totalValue.toFixed(2),
    "Commission": commission.toFixed(2),
    "Items Ordered": JSON.stringify(orderedItems),
    "Discount Applied": discount
  };

  // Form Submission Logic for Spreadsheet
  $.ajax({
    url: "https://api.apispreadsheets.com/data/Avxu9d3EgmuC2yze/",
    type: "post",
    data: formData,
    headers: {
      accessKey: "6ddd68bf0a056a75dde252f1d63fade0",
      secretKey: "5e991c192560d0eb3ee1951fb03c7675",
      "Content-Type": "application/x-www-form-urlencoded",
    },
    success: function () {
      alert("Form Data Submitted to Spreadsheet and Discord :)");
      resetForm();
    },
    error: function () {
      alert("There was an error :(");
    }
  });

  // Prepare data for Discord webhook
  const discordData = {
    username: "Receipts",
    content: `New order submitted by ${employeeName}`,
    embeds: [{
      title: "Order Details",
      fields: [
        { name: "Employee Name", value: employeeName, inline: true },
        { name: "Total", value: `$${totalValue.toFixed(2)}`, inline: true },
        { name: "Commission", value: `$${commission.toFixed(2)}`, inline: true },
        { name: "Discount Applied", value: `${discount}%`, inline: true },
        { name: "Items Ordered", value: orderedItems.map(item => `${item.quantity}x ${item.name}`).join('\n') }
      ],
      color: 0x00ff00 // You can customize the color
    }]
  };

  // Form Submission Logic for Discord webhook
  $.ajax({
    url: "https://discord.com/api/webhooks/1225543759765176360/7BfZLU7nRNbotXWIBxF5pvf9BVnWnb8cy5xB1gZgTectcrprhbTnSXTBS4qeJK4bjQQs", // Replace with your Discord webhook URL
    type: "post",
    contentType: "application/json",
    data: JSON.stringify(discordData),
    success: function () {
      // Do nothing specific for Discord success
    },
    error: function () {
      console.error("Error sending data to Discord :(");
    }
  });

  // Reset checkboxes and quantity inputs
  $('.menu-item').prop('checked', false);
  $('.quantity').val(1);

  // Reset totals
  document.getElementById('total').innerText = '';
  document.getElementById('commission').innerText = '';
  // Reset discount dropdown to default
  $("#discount").val("0");
}

    function resetForm() {
      // Reset checkboxes and quantity inputs
      $('.menu-item').prop('checked', false);
      $('.quantity').val(1);

      // Reset totals
      document.getElementById('total').innerText = '';
      document.getElementById('commission').innerText = '';
      // Reset discount dropdown to default
      $("#discount").val("0");
    }
  </script>
</head>
<body>

  <h2>GoldMine Pawnshop Menu</h2>

  <form id="menuForm">
	  <h3>Ancient Amulet/h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Min Sell Price - $88
      <input type="number" class="quantity" value="1" min="1" />
    </label>
		     <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Max Sell Price - $135
      <input type="number" class="quantity" value="1" min="1" />
    </label>
      <h3>Ancient Blade/h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Min Sell Price - $88
      <input type="number" class="quantity" value="1" min="1" />
    </label>
		     <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Max Sell Price - $135
      <input type="number" class="quantity" value="1" min="1" />
    </label>
      <h3>Anceint Guard/h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Min Sell Price - $88
      <input type="number" class="quantity" value="1" min="1" />
    </label>
		     <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Max Sell Price - $135
      <input type="number" class="quantity" value="1" min="1" />
    </label>
      <h3>Ancient Hilt/h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Min Sell Price - $88
      <input type="number" class="quantity" value="1" min="1" />
    </label>
		     <label>
      <input type="checkbox" class="menu-item" data-price="1000" /> GM Max Sell Price - $135
      <input type="number" class="quantity" value="1" min="1" />
    </label>

	
	
	
	
	<div style="margin-bottom: 30px;"></div>
	
	<label for="discount">Select Discount:</label>
    <select id="discount" onchange="calculateTotals()">
      <option value="0">No Discount</option>
      <option value="40">40% Discount (Employee Discount)</option>
      <option value="30">30% Discount (LEO Discount)</option>
    </select>
	
	<div style="margin-bottom: 30px;"></div>
	


    <label for="employeeName">Employee Name:</label>
    <input type="text" id="employeeName" required="" />
	
	<div style="margin-bottom: 30px;"></div>
	
	

    <p>Total: $<span id="total"></span></p>
    <p>Commission (20%): $<span id="commission"></span></p>
	
	<div style="margin-bottom: 30px;"></div>

    <button type="button" onclick="calculateTotals()">Calculate</button>
    <button type="button" onclick="SubForm()">Submit</button>
    <button type="button" onclick="resetForm()">Reset</button>
    </div>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/anchor-js/4.1.0/anchor.min.js" integrity="sha256-lZaRhKri35AyJSypXXs4o6OPFTbTmUoltBbDCbdzegg=" crossorigin="anonymous"></script>
    <script>anchors.add();</script>
  </body>
</html>
