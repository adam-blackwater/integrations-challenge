# Connections - Processors                      

This documentation explains how to run the connections processors code. There are some settings that need to be altered in order to do this.

The API key that is included in the `Stripe.ts` file is valid and you can use it if you wish.

The style guide for formatting this code can be found [here](https://github.com/basarat/typescript-book/blob/master/docs/styleguide/styleguide.md).

## Set Up

### Set Stripe to accept non-tokenized card numbers over the Stripe API.

Go to the [integrations](https://dashboard.stripe.com/account/integration/settings) page for your Stripe account. Here you can tell the Stripe API to accept non-tokenized card numbers.

### Authorizing against cards with different properties

Stripe offers test cards that will return varying responses.

Replace the value of `cardNumber` in `main.ts` with a card number on the list found [here](https://stripe.com/docs/testing#cards-responses).

#### Useful card numbers

  - `4111111111111111`: an accepted card number
  - `4000000000000002`: a declined card number

## Notes 

The stripe API accepts `form-data`. When adding methods that send `POST` data with bodies, encode the data you wish to send as `form-data` and set the `Content-Type` header to `application/x-www-form-urlencoded`.

## POST

### authorize(): Promise\<ParsedAuthorizationResponse\>
Some parameters are set in the `POST` data to achieve the desired `PaymentIntent` confirm and capture behaviour.

#### The `confirm` parameter

Setting the `confirm` parameter to `true` allows the `paymentIntent` to be captured with no further confirmation action needed on the `paymentIntent`.

If this parameter is not set to `true` the `paymentIntent` status will be set to `requires_confirmation` and will not be capture-able until confirmed. See [here](https://stripe.com/docs/api/payment_intents/create#create_payment_intent-confirm) for more details. 

#### The `capture_method` parameter

The Stripe API is configured to automatically set a `paymentIntent` to `captured` immediately after they are authorized. This behaviour can be overridden by setting the `capture_method` parameter to `manual`.

#### The `payment_method_data` parameter

To add payment methods that are not registered on a Stripe account the `payment_method_data` parameter will accept payment method details. 
 
This is a hash object which must be provided a  `type` key (in this case it is hard-coded to `card`). On this hash a `card object` may be attached. Details can be found [here](https://stripe.com/docs/api/cards/object).

The result of setting the parameter `payment_method_data` is the `payment_method` parameter is set with the payment details you provide. Details such as card number and expiration year can now be set. See [here](https://stripe.com/docs/api/payment_intents/create#create_payment_intent-payment_method_data) for more details.

#### Response

The `paymentIntent` object is described [here](https://stripe.com/docs/api/payment_intents/object).

The `status` parameter on the returned `paymentIntent` object will be set to `requires_capture`,

### capture(): Promise\<ParsedCaptureResponse\>

Requires the `paymentIntent` id of the `paymentInten` you wish to capture. 


#### Response

The `paymentIntent` object is described [here](https://stripe.com/docs/api/payment_intents/object).

The `status` parameter on the returned `paymentIntent` object will be set to `succeeded`.

### cancel(): Promise\<ParsedCancelResponse\>

Requires the `paymentIntent` id of the `paymentInten` you wish to cancel.


#### Response

The `paymentIntent` object is described [here](https://stripe.com/docs/api/payment_intents/object).

The `status` parameter on the returned `paymentIntent` object will be set to `canceled`.
