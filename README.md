<p align="center"><img width="45%"  src="https://github.com/delionAPI/delion-payment-client/raw/master/delion-logo.png"></p>

----

A delion client is a modified clone of zoid framework [zoid](http://krakenjs.com/zoid/demo/index.htm)

A cross-domain delion client, supporting:

- Render an popup on a different domain, and pass down props, including objects and functions
- Call callbacks natively from the child window without worrying about post-messaging or cross-domain restrictions
- Create and expose components to share functionality from your site to delion!


-----

### [Demo Sandbox](https://console.delion.io/paymentobjects/sandbox/index.html)
### [Demo Production](https://console.delion.io/paymentobjects/production/index.html)

Working demos of different stages

-----

### Quick example


Render the component on the parent page:

```javascript
<head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-material-design/4.0.2/bootstrap-material-design.css" /> 
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<script src="https://console.delion.io/paymentobjects/payment-0.0.9.js"></script>
	<style>
		body {text-align: center;}
		h3 {margin: 50px;}
	</style>
</head>

<body>	
    <h3>Example Shop (Production)</h3>
	
	<div id="cart-content"> 
		<div>Cart:</div> 
		<div>Product description: <input id="input-description" type="text" value="IOTA sticker"></div>
		<div>Price in IOTA: <input id="input-price" type="text" value="1240"></div>
		<div>Recipient: <input id="input-recipient" type="text" value="user@example.com"></div>
	</div>

    <!-- Example button Button to open the Delion popup and execute payment --> 
	<div id="popupLogin" style="border: 1px solid grey;margin-top:20px;" class="btn btn-primary">pay with Delion</div>

    <!-- Container to display the result of the login call -->
	<div id="delion-payment-container"></div>
	
    <script>

        document.querySelector('#popupLogin').addEventListener('click', function () {

            // select total products price from cart
			const price=document.querySelector('#input-price').value;
			// select cart description
			const description=document.querySelector('#input-description').value;
			// select recipient/shop owner
			const recipient=document.querySelector('#input-recipient').value;

			// Render delion payment component and pass down props to the popup
            delion.payment.render({	
                
				env: 'production', //'demo', 'sandbox', 'production'

                /*prefilledPayerEmail: '',*/  //fill with payer delion email if known
				
				prefilledPaymentObject: {
					transactions:[
						{
							recipient_user: recipient,         //recipient user email
							description: description,          // payment description
                            amount: { total: price }           // total price
                        }
					]
				},
				
				// [REQUIRED] When the iframe window calls `window.xprops.onLogin(response)` this callback will be called
                onLogin: function(response) {
					// example: create 'div' allement and append this to '#delion-payment-container'
					var el = document.createElement("DIV"); 
					el.innerText= 'onLogin-Response:'+response;	
					document.querySelector('#delion-payment-container').appendChild(el); 	
                },
				
				// [REQUIRED] When the iframe window calls `window.xprops.onExecutePayment(response)` this callback will be called. 
				onExecutePayment: function(response) {
					// example: create 'div' allement and append this to '#delion-payment-container'
					var el = document.createElement("DIV"); 
					el.innerText= 'onExecutePayment-Response:'+response;	
					document.querySelector('#delion-payment-container').appendChild(el); 
					
					const respObject=JSON.parse(response);
					if(respObject.status!=null && respObject.status=='ok'){
						var cartContent = document.getElementById("cart-content");
						cartContent.innerHTML = '';
						var thxDiv = document.createElement("DIV");
						thxDiv.innerText= 'Thank you for your purchase!(PayID:'+respObject.id+')';	
						cartContent.appendChild(thxDiv);
						/*
							execute custom backend steps. For examle:
							- post cart and Delion payid to backend
							- compare and validate payid content(...[GET]/payments/PAYID-II...) and amount with the cart content(fraud protection)
							- ..
						*/
					}		
					
                },
				
				// [OPTIONAL]  
				onCreatePayment: function(response) {
                    // response after create payment  
					// example: create 'div' allement and append this to '#delion-payment-container'
					var el = document.createElement("DIV"); 
					el.innerText= 'onCreatePayment-Response:'+response;	
					document.querySelector('#delion-payment-container').appendChild(el); 					
                }
					
				/* 
				// [OPTIONAL]  
				onExit: function(response) {
                    // create abort statistics 				
                }
				*/
            },'#delion-payment-container');
        });
    </script>
</body>
```


### Useful Links

- [Introducing zoid](https://medium.com/@bluepnume/introducing-zoid-seamless-cross-domain-web-components-from-paypal-c0144f3e82bf#.ikbg9r1ml)


### Supported Paymentflow 



### Browser Support

- Internet Explorer 9+
- Chrome 27+
- Firefox 30+
- Safari 5.1+
- Opera 23+
