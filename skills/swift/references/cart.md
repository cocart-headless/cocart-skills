# Cart Operations

## Common Operations

```swift
let client = CoCart(url: "https://your-store.com")

// Add item
try await client.cart().addItem(productId: 123, quantity: 2)

// Add item with variation
try await client.cart().addItem(productId: 123, quantity: 1, variationId: 456)

// Get cart
let cart = try await client.cart().get()
let items = cart.getItems()
let total = cart.get("totals.total")

// Update item quantity
try await client.cart().updateItem(itemKey, quantity: 3)

// Remove item
try await client.cart().removeItem(itemKey)

// Apply coupon
try await client.cart().applyCoupon("SAVE10")

// Remove coupon
try await client.cart().removeCoupon("SAVE10")

// Clear cart
try await client.cart().clear()
```

## Currency Formatting

```swift
let formatter = CurrencyFormatter()
let cart = try await client.cart().get()
let currency = cart.getCurrency()

print(formatter.format(4599, currency: currency))        // "$45.99"
print(formatter.formatDecimal(4599, currency: currency)) // "45.99"
```

## Sessions API (admin)

```swift
let admin = CoCart(
    url: "https://your-store.com",
    config: CoCartConfig(
        consumerKey: "ck_xxxxx",
        consumerSecret: "cs_xxxxx"
    )
)

let sessions = try await admin.sessions().all()
let session = try await admin.sessions().get(cartKey)
try await admin.sessions().delete(cartKey)
```

## Error Handling

```swift
do {
    try await client.cart().addItem(productId: -1, quantity: 0)
} catch let error as ValidationError {
    print("Validation: \(error.message)")
} catch let error as AuthenticationError {
    print("Auth: \(error.message)")
} catch let error as NetworkError {
    print("Network: \(error.message)")
} catch let error as CoCartError {
    print("API error: \(error.message) (code: \(error.code))")
}
```

## SwiftUI Example

```swift
import SwiftUI
import CoCart

struct CartView: View {
    @State private var items: [[String: Any]] = []
    @State private var isLoading = true
    @State private var error: Error?

    let client = CoCart(url: "https://your-store.com")

    var body: some View {
        NavigationStack {
            Group {
                if isLoading {
                    ProgressView()
                } else if let error {
                    ContentUnavailableView(
                        "Failed to load cart",
                        systemImage: "cart",
                        description: Text(error.localizedDescription)
                    )
                } else {
                    List(items.indices, id: \.self) { index in
                        let item = items[index]
                        HStack {
                            Text(item["name"] as? String ?? "")
                            Spacer()
                            Text("Qty: \(item["quantity"] as? Int ?? 0)")
                        }
                    }
                }
            }
            .navigationTitle("Cart")
            .task {
                await loadCart()
            }
        }
    }

    func loadCart() async {
        do {
            let cart = try await client.cart().get()
            items = cart.getItems()
            isLoading = false
        } catch {
            self.error = error
            isLoading = false
        }
    }
}
```

## Environment Variables

```swift
// Use Bundle info or xcconfig for non-secret config
let storeURL = Bundle.main.infoDictionary?["STORE_URL"] as? String ?? ""
let client = CoCart(url: storeURL)

// Use Keychain for sensitive keys — never commit credentials
let client = CoCart(
    url: storeURL,
    config: CoCartConfig(
        consumerKey: KeychainHelper.get("COCART_CONSUMER_KEY"),
        consumerSecret: KeychainHelper.get("COCART_CONSUMER_SECRET")
    )
)
```

## Further Reading

- [SDK Repository](https://github.com/cocart-headless/cocart-swift-sdk)
- [CoCart Docs](https://docs.cocartapi.com/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
