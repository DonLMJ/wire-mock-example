# Wiremock example

This project contains examples of wiremock stubs that use advanced Wiremock capabilities, namely:
    - advanced response bodies using handlebars templates
    - statefull behaviour using scenarios
    - and advanced statefull behavioru using wiremock-state-extension

## How to exercise this example

You can test the various stubs with the following requests:

- Create Order
  - exercises the handlebars template that assembles the response body (see: `wiremock/__files/create_order.handlebars`)
  - stores the order response in the state cache

  ```bash
  curl --request POST \
    --url http://localhost:11091/order \
    --header 'Content-Type: application/json' \
    --data '{
    "description": "My first order",
    "currency": "pound",
    "lines": [
      {
        "item": "Aspirin",
        "units": 5
      },
      {
        "item": "Hydrogen peroxide",
        "units": 5
      },
      {
        "item": "Palmolive Shampoo",
        "units": 10
      },
          {
        "item": "Paracetamol",
        "units": 15
      }
    ]
  }
  '
  ```

- Get order
  - exercises the handlebars template that assembles the response body (see: `wiremock/__files/get_order.handlebars`)
  - retrieves the order from the state cache
  - **replace `:orderId` with the orderId returned in the create request**

  ```bash
  curl --request GET \
  --url http://localhost:11091/:orderId
  ```

- Update order
  - retrieves the order from the state cache and assembles a response with the new state (see: `wiremock/__files/patch_order.handlebars`)
  - **replace `:orderId` with the orderId returned in the create request**

  ```bash
  curl --request PATCH \
    --url http://localhost:11091/order/9f87:orderId \
    --header 'Content-Type: application/json' \
    --data '{
    "state":"CANCELED"
  }'
  ```

- Delete order
  - clears the order from the state cache
  - **replace `:orderId` with the orderId returned in the create request**

  ```bash
  curl --request DELETE \
  --url http://localhost:11091/order/:orderId
  ```

- Retry scenario
  - demoes wiremock statefull behaviour using scenarios

  ```bash
  curl --request DELETE \
    --url http://localhost:11091/order/34bfcc0c-dc2e-48e6-a438-5b1fd9a1db31 \
    --header 'X-Stub-Type: scenario'
  ```

- Reset scenario
  - demoes how to reset the state of all scenarios

  ```bash
  curl --request POST \
    --url http://localhost:11091/__admin/scenarios/reset
  ```
  