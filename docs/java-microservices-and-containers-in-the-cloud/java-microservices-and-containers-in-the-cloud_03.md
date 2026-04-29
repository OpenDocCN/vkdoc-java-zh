# The Root Query for the application
type Query {
products(count: Int, offset: Int): [Product]!
}
