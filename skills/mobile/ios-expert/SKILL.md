---
name: ios-expert
description: Expert in iOS development with Swift, SwiftUI, UIKit, Combine, async/await, and Apple platform best practices. Use for building iOS apps, widgets, extensions, and Apple ecosystem integrations.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# iOS Development Expert

## Purpose
Provide expert-level iOS development assistance focusing on Swift, SwiftUI, UIKit, app architecture, performance optimization, and Apple platform best practices.

## When to Use This Skill
- Building iOS apps with SwiftUI or UIKit
- Implementing navigation, state management, and data flow
- Working with Core Data, SwiftData, or other persistence frameworks
- Integrating Apple frameworks (MapKit, HealthKit, StoreKit, CloudKit, etc.)
- Performance optimization and memory management
- App Store submission and distribution
- Writing unit and UI tests for iOS
- Building widgets, extensions, and Watch/TV companions

## Key Principles

### 1. Modern Swift Patterns
- Prefer SwiftUI for new views, UIKit when SwiftUI falls short
- Use Swift concurrency (async/await, actors, TaskGroups) over GCD/closures
- Leverage value types (structs, enums) over reference types where possible
- Use protocol-oriented programming and protocol extensions
- Apply property wrappers effectively (@State, @Binding, @Published, @AppStorage)

### 2. SwiftUI Best Practices
- Keep views small and composable
- Extract subviews and view modifiers for reuse
- Use @StateObject for owned observable objects, @ObservedObject for injected ones
- Prefer @Observable (Observation framework) on iOS 17+
- Use environment values and preferences for view communication
- Implement proper previews for rapid iteration

### 3. UIKit Best Practices
- Use Auto Layout with constraints (programmatic preferred over storyboards)
- Implement UICollectionView with DiffableDataSource and compositional layouts
- Follow coordinator or router patterns for navigation
- Manage memory with proper retain cycle prevention (weak/unowned)
- Use UIViewRepresentable to bridge UIKit into SwiftUI

### 4. Architecture Patterns
- MVVM as default architecture for SwiftUI apps
- Clean Architecture or VIPER for large-scale apps
- Dependency injection via initializer injection or environment
- Repository pattern for data access abstraction
- Use cases / interactors for business logic isolation

### 5. Concurrency & Networking
- Use async/await for all asynchronous code
- Actors for thread-safe mutable state
- Structured concurrency with TaskGroups for parallel work
- URLSession with async/await for networking
- Combine for reactive streams where appropriate

## Code Structure

### SwiftUI View Template
```swift
import SwiftUI

struct ContentView: View {
    @StateObject private var viewModel = ContentViewModel()
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            List(viewModel.items) { item in
                ItemRow(item: item)
            }
            .navigationTitle("Items")
            .toolbar {
                ToolbarItem(placement: .primaryAction) {
                    Button("Add", systemImage: "plus") {
                        viewModel.addItem()
                    }
                }
            }
            .task {
                await viewModel.loadItems()
            }
            .refreshable {
                await viewModel.loadItems()
            }
            .alert("Error", isPresented: $viewModel.showError) {
                Button("OK") {}
            } message: {
                Text(viewModel.errorMessage)
            }
        }
    }
}

#Preview {
    ContentView()
}
```

### ViewModel Template
```swift
import Foundation
import Observation

@Observable
final class ContentViewModel {
    var items: [Item] = []
    var isLoading = false
    var showError = false
    var errorMessage = ""

    private let repository: ItemRepositoryProtocol

    init(repository: ItemRepositoryProtocol = ItemRepository()) {
        self.repository = repository
    }

    func loadItems() async {
        isLoading = true
        defer { isLoading = false }

        do {
            items = try await repository.fetchItems()
        } catch {
            errorMessage = error.localizedDescription
            showError = true
        }
    }

    func addItem() {
        let newItem = Item(title: "New Item", createdAt: .now)
        items.append(newItem)
    }
}
```

### Networking Layer Template
```swift
import Foundation

protocol APIClientProtocol {
    func request<T: Decodable>(_ endpoint: Endpoint) async throws -> T
}

struct APIClient: APIClientProtocol {
    private let session: URLSession
    private let decoder: JSONDecoder

    init(session: URLSession = .shared) {
        self.session = session
        self.decoder = JSONDecoder()
        self.decoder.dateDecodingStrategy = .iso8601
        self.decoder.keyDecodingStrategy = .convertFromSnakeCase
    }

    func request<T: Decodable>(_ endpoint: Endpoint) async throws -> T {
        let (data, response) = try await session.data(for: endpoint.urlRequest)

        guard let httpResponse = response as? HTTPURLResponse else {
            throw APIError.invalidResponse
        }

        guard (200...299).contains(httpResponse.statusCode) else {
            throw APIError.httpError(statusCode: httpResponse.statusCode)
        }

        return try decoder.decode(T.self, from: data)
    }
}

enum APIError: LocalizedError {
    case invalidResponse
    case httpError(statusCode: Int)

    var errorDescription: String? {
        switch self {
        case .invalidResponse: "Invalid server response"
        case .httpError(let code): "Server error (HTTP \(code))"
        }
    }
}
```

## Common Patterns

### 1. SwiftData Persistence (iOS 17+)
```swift
import SwiftData

@Model
final class Task {
    var title: String
    var isCompleted: Bool
    var createdAt: Date

    init(title: String, isCompleted: Bool = false) {
        self.title = title
        self.isCompleted = isCompleted
        self.createdAt = .now
    }
}

// In App
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: Task.self)
    }
}

// In View
struct TaskListView: View {
    @Query(sort: \Task.createdAt, order: .reverse) var tasks: [Task]
    @Environment(\.modelContext) private var context

    var body: some View {
        List(tasks) { task in
            TaskRow(task: task)
        }
    }
}
```

### 2. Navigation with NavigationStack
```swift
enum Route: Hashable {
    case detail(Item)
    case settings
    case profile(userId: String)
}

struct RootView: View {
    @State private var path = NavigationPath()

    var body: some View {
        NavigationStack(path: $path) {
            HomeView()
                .navigationDestination(for: Route.self) { route in
                    switch route {
                    case .detail(let item): DetailView(item: item)
                    case .settings: SettingsView()
                    case .profile(let id): ProfileView(userId: id)
                    }
                }
        }
    }
}
```

### 3. Dependency Injection via Environment
```swift
private struct RepositoryKey: EnvironmentKey {
    static let defaultValue: ItemRepositoryProtocol = ItemRepository()
}

extension EnvironmentValues {
    var itemRepository: ItemRepositoryProtocol {
        get { self[RepositoryKey.self] }
        set { self[RepositoryKey.self] = newValue }
    }
}

// Usage in views
struct ItemListView: View {
    @Environment(\.itemRepository) private var repository
}
```

### 4. Error Handling Pattern
```swift
enum AppError: LocalizedError {
    case networkUnavailable
    case unauthorized
    case notFound
    case serverError(underlying: Error)

    var errorDescription: String? {
        switch self {
        case .networkUnavailable: "No internet connection"
        case .unauthorized: "Please sign in again"
        case .notFound: "Content not found"
        case .serverError: "Something went wrong"
        }
    }

    var recoverySuggestion: String? {
        switch self {
        case .networkUnavailable: "Check your connection and try again"
        case .unauthorized: "Your session has expired"
        default: "Please try again later"
        }
    }
}
```

## Best Practices

1. **Project Organization**
   - Feature-based folder structure over type-based
   - Separate UI, domain, and data layers
   - Use Swift Package Manager for modularization
   - Co-locate views, view models, and models per feature

2. **Performance**
   - Profile with Instruments before optimizing
   - Use lazy loading for expensive views (LazyVStack, LazyHStack)
   - Avoid unnecessary redraws — minimize @State mutations
   - Cache images with AsyncImage or dedicated libraries
   - Use background contexts for Core Data / SwiftData writes

3. **Testing**
   - Unit test view models and business logic
   - Use protocols for dependency injection and mocking
   - UI test critical user flows with XCUITest
   - Snapshot testing for visual regression
   - Test async code with Swift Testing framework

4. **Accessibility**
   - Add meaningful accessibility labels and hints
   - Support Dynamic Type for all text
   - Ensure proper VoiceOver navigation order
   - Test with Accessibility Inspector
   - Support reduced motion preferences

5. **App Lifecycle**
   - Handle scene phases (@Environment(\.scenePhase))
   - Save state on background transition
   - Handle deep links and universal links
   - Support push notifications properly
   - Implement proper onboarding flows

## Anti-Patterns to Avoid

- Massive view bodies — extract subviews
- Business logic in views — move to view models
- Force unwrapping optionals in production code
- Ignoring retain cycles in closures (use [weak self])
- Using DispatchQueue when async/await is available
- Putting networking code directly in views
- Hardcoding strings — use String Catalogs for localization
- Ignoring the main actor for UI updates

## Tools & Frameworks

- **UI**: SwiftUI, UIKit, AppKit (macOS)
- **Persistence**: SwiftData, Core Data, UserDefaults, Keychain
- **Networking**: URLSession, Alamofire
- **Reactive**: Combine, Observation framework
- **Testing**: XCTest, Swift Testing, XCUITest
- **Dependencies**: Swift Package Manager
- **CI/CD**: Xcode Cloud, Fastlane, GitHub Actions
- **Analytics**: Firebase, Mixpanel, TelemetryDeck
- **Payments**: StoreKit 2

## Implementation Approach

When implementing iOS features:

1. **Analyze Requirements** — Understand the feature scope and Apple platform capabilities
2. **Design Architecture** — Plan data flow, navigation, and component structure
3. **Define Models** — Create data models and protocols first
4. **Implement ViewModel** — Build business logic with testability in mind
5. **Build Views** — Compose SwiftUI views, keeping them focused and small
6. **Handle Edge Cases** — Empty states, errors, loading, offline mode
7. **Test** — Unit test logic, UI test critical flows
8. **Optimize** — Profile with Instruments, fix performance issues

This skill ensures production-ready, maintainable, and performant iOS applications following Apple's latest guidelines and modern Swift patterns.
