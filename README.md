SPRI
====

simple probabilistic resource identifier

# abstract

an experiment on a digital resource identification scheme with distributability and extensibility in mind.

in a nutshell, all we are doing here is to define a system of rules to characterize a unified search query in a semi-human-friendly way, expecting some degree of lossy input (including typos), and defining the expect behavior of a service provider that uses such a search query.

the query results should only be *metadata*, but the metadata should uniquely and authoritatively describe the resource.

## motivation

### citation formats are obsolete

standardized styles like the APA were helpful for consumers when there are many resources in reference; standardization gives structure, and makes the content more manageable. Nowadays it doesn't matter what style is used -- we just need keywords. The more, the better. Paste the keywords into a search engine, and you *should* find the resource. Basically we want a standard citation that operates under this premise, that canonicalizes the referencing format, is suitable for fast processing by a computerized service, but also preserves information that is human-friendly.

### stable citation / anchoring for unstable or incomplete resources

it should operate akin to the DOI, except handle arbitrary resource types, and allow distributed control of discovery

for example, there are many publications that don't have DOIs. we want a system that can reliably generate a stable reference for it.

if a SPRI service provider goes down and loses all its data, a new SPRI service must handle the same identifier for the same resource when it is re-imported

### amenable to change, while still providing 'unique' resource identifiers

conceptually, this is the same as a search engine that always reports the same results.

the difference is the resources are assumed to be stable and immutable.

### democratize input

amenable to wikification of resource entry, and access permissions if needed.

## constraints

### sequential identifiers and random strings

can't use auto increment keys and strings like UUID. Given 2 instances of the service, we want loosely similar queries to return loosely the same results, assuming their databases overlap in the query range

### usage of hashes

can't use content hashes. consider a book that has 3 editions. they are the 'same' book.

the desirable function is, if you query the book's title, you get a **list** of books back, sorted by the commonness of the book. in this case, it might be the 2nd edition first, 1st edition second, and 3rd edition last.

### multiple languages

in the current example (version 1), "spri://1." should be the only segment that must be latin-alphabet-constrained. As such, a client conforming to SPRI.1 need not look anything like a resource identifier string to the user interacting with the client

# expected behavior / examples

## big picture

we will always return lists. for any client looking for the exact match, that will be equivalent to taking the first hit aka "I'm feeling lucky".

This should work for the vast majority of cases, and when it doesn't, the client should always have functionality to quickly pick subsequent matches.

the query segments should always be ordered by general to specific, and ordered *temporally* within specificity. In the case of the English alphabet, A comes before Z. A Romanized query thus should be alphabetically ordered within specificity. A non-romanized query should follow the temporal order of its own script.

### A hypothetical case

if a user searches for a book, knowing of only 1 version of that book, they expect the SPRI of that book, which is actually "book version 1". It turns out the most representative book is actually version 2 now, so we return version 2. At this point, the user should update their belief about what represents the book in this world. They should think, "oh, my search leads to book version 2 now". Then they think, "but I am actually looking specifically for version 1". So at this point they should express "version 1" in their SPRI.

### uniqueness, mutability, and queryability

querying for "time right now" will give different results for different times and locations. the result should theoretically give that time, for that user. But since there should not be intelligence in describing a state, the service should not handle a state that is inherently mutable.

## version 1

spri://1/

- for version 1+, the first segment until the first slash will be the version number
- if the first segment is not an integer, default to the latest version

### URL

- given: http://en.wikipedia.org/wiki/List_of_best-selling_books
- spri: spri://1/wikipedia/english/List_of_best-selling_books

the server should return the exact URL above as its first hit

to humans, this should be like telling a friend, "check out the list of best selling books on wikipedia". do you remember the exact URL? No, but if you ask SPRI it *should* give you the right URL.

To simplify, spri://1/wikipedia/list_of_best-selling_books should also give the same URL as its first hit, based on the English wikipedia being the largest and thus globally top-of-mind.

the burden of logic is on the *SPRI PROVIDER* to know this about wikipedia.

the SPRI provider should know about the resource in question. If there is no knowledge of the type of resource, it must be maximally specified in the query.

- given: https://github.com
- spri: spri://1/github

note we should return HTTPS because github defaults to it.

- given: http://www.wikipedia.org
- spri: spri://1/wikipedia

the first hit should be http://www.wikipedia.org, and a later hit should be https://www.wikipedia.org, whichever is most popular

what happens when popularity switches? We should switch as well. If the service is democratically managed, then there will be a lag, but a strong expectation of convergence.

### book

examples taken from the best-selling books URL above

- given: A Tale of Two Cities
- spri: spri://1/dickens/tale_of_two_cities
- spri: spri://1/book/dickens/tale_of_two_cities

should expect similar results

- given: Dream of the Red Chamber
- spri: spri://1/book/dream_of_the_red_chamber

should describe the english version of 紅樓夢, whereas

- given: 紅樓夢
- spri: spri://1/book/紅樓夢

should describe the Traditional-Chinese one.

### academic journal

The "most cited paper". Taking 3 citation styles from google scholar, this paper should be queried by a single SPRI query

- MLA: Lowry, Oliver H., et al. "Protein measurement with the Folin phenol reagent." J biol chem 193.1 (1951): 265-275.
- APA: Lowry, O. H., Rosebrough, N. J., Farr, A. L., & Randall, R. J. (1951). Protein measurement with the Folin phenol reagent. J biol chem, 193(1), 265-275.
- Chicago: Lowry, Oliver H., Nira J. Rosebrough, A. Lewis Farr, and Rose J. Randall. "Protein measurement with the Folin phenol reagent." J biol chem 193, no. 1 (1951): 265-275.

these should all work (i.e. give the same first hit; later hits may not be the same)

-_spri:_spri://1/1951/lowry,rosebrough/protein_measurement_with_the_folin_phenol_reagent
-_spri:_spri://1/1951/lowry/protein_measurement_with_the_folin_phenol_reagent
-_spri:_spri://1/1951/lowry/protein_measurement_with_the_folin_phenol_reagent
-_spri:_spri://1/1951/lowry/protein_measurement_folin_phenol
-_spri:_spri://1/1951/protein_measurement_with_the_folin_phenol_reagent
-_spri:_spri://1/lowry/protein_measurement_with_the_folin_phenol_reagent
-_spri:_spri://1/protein_measurement_with_the_folin_phenol_reagent


