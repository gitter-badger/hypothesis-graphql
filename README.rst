hypothesis-graphql
==================

Hypothesis strategies for GraphQL schemas, queries and data.

**NOTE** This package is experimental, many things don't work yet and documented in a way they are planned to be used.

Usage
-----

There are four strategies for different use cases.

1. Schema generation - ``hypothesis_graphql.strategies.schema()``
2. Query - ``hypothesis_graphql.strategies.query(schema)``.
3. Response for a query - ``hypothesis_graphql.strategies.response(schema, query)``
4. Data for a type - ``hypothesis_graphql.strategies.data(schema, type_name)``

At the moment only ``schema`` & ``query`` are working with some limitations.

Lets take this schema as an example:

.. code::

    type Book {
      title: String
      author: Author
    }

    type Author {
      name: String
      books: [Book]
    }

    type Query {
      getBooks: [Book]
      getAuthors: [Author]
    }

Then strategies might be used in this way:

.. code:: python

    from hypothesis import given
    from hypothesis_graphql import strategies as gql_st

    SCHEMA = """..."""  # the one above

    @given(query=gql_st.query(schema))
    def test_query(query):
        ...
        # This query might be generated:
        #
        # query {
        #   getBooks {
        #     title
        #   }
        # }

    @given(response=gql_st.response(schema, query))
    def test_response(response):
        ...
        # Example response with a query from the example above:
        #
        # {
        #   "data": {
        #     "getBooks": [
        #       {"title": "War and Peace"}
        #     ]
        #   }
        # }

    @given(data=gql_st.data(schema, "Book"))
    def test_data(data):
        ...
        # Example data:
        #
        # {
        #   "title": "War and Peace"
        #   "author": {"name": "Leo Tolstoy"}
        # }