#  <img src=".\logo\avanscoperta_monogram.png" alt="avanscoperta_monogram" style="zoom:5%;" /> Deep Dive into Model-Driven Design 

**Website**: https://www.avanscoperta.it/en/training/deep-dive-into-model-driven-design-workshop/

**Trainer: Bruno Boucard** and **Kenny Baas-Schwegler**

Instructions to implement labs with the **Supple Design** and other labs **Deep Modeling**, here are detailed instructions for each approach:

## Session Three - Supple Design

Supple Design is an approach that aims to make code fluid, clear, and easy to understand to reduce accidental complexity. Here are the main steps:

#### Steps for Supple Design:
**Use Explicit Methods**: Create well-named methods that clearly express intent. For example, instead of generic methods like `process`, use more descriptive names like `calculateDiscount`.

**Introduce Value Objects**: Encapsulate important concepts in objects without identity, like `Money`, DateRange`, or `Coordinates`. This makes the code more readable and reduces the risk of errors.

**Favor Small Services and Modules**: Divide complex parts into specialized services or modules with specific responsibilities to avoid "god objects" that are overly complex.

**Intentional Design**: Use design patterns such as **Domain Service** (for specific operations) and **Policy Objects** (to encapsulate complex business rules) to make the model more flexible and understandable.

### Supple Design

#### Value Object

In general, a Value Object is a small, immutable object that represents a descriptive aspect of the domain with no conceptual identity. Using Value Objects  helps keep the domain model clean, focused, and expressive, while ensuring that key aspects of the domain logic are captured in a modular and reusable way.

**You should start implementing Supple Design by testing the seat class to transform it into a value object**

##### SeatTest.Java

```java
public class SeatingPlaceTest {

    @Test
    public void Be_a_Value_Type() {
        SeatingPlace firstInstance = new SeatingPlace("A", 1, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);
        SeatingPlace secondInstance = new SeatingPlace("A", 1, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);

        // Two different instances with same values should be equals
        assertThat(secondInstance).isEqualTo(firstInstance);

        // Should not mutate existing instance
        secondInstance.allocate();
        assertThat(secondInstance).isEqualTo(firstInstance);
    }
}
```

##### SeatingPlaceShould.cs

```c#
[TestFixture]
public class SeatingPlaceShould
{
    [Test]
    public void Be_a_Value_Type()
    {
        var firstInstance = new SeatingPlace("A", 1, PricingCategory.Second, SeatingPlaceAvailability.Available);
        var secondInstance = new SeatingPlace("A", 1, PricingCategory.Second, SeatingPlaceAvailability.Available);

        // Two different instances with same values should be equals
        Check.That(secondInstance).IsEqualTo(firstInstance);

        // Should not mutate existing instance 
        secondInstance.Allocate();
        Check.That(secondInstance).IsEqualTo(firstInstance);
    }    
}
```

Then, you should continue with testing the Row class to transform it into a value object.

##### RowTest.Java

```java
public class RowTest {
    @Test
    public void Be_a_Value_Type() {
        SeatingPlace firstInstance = new SeatingPlace("A", 1, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);
        SeatingPlace secondInstance = new SeatingPlace("A", 1, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);

        // Two different instances with same values should be equals
        assertThat(secondInstance).isEqualTo(firstInstance);

        // Should not mutate existing instance
        secondInstance.allocate();
        assertThat(secondInstance).isEqualTo(firstInstance);
    }
}
```

##### RowShould.cs

```C#
[TestFixture]
public class RowShould
{
    [Test]
    public void Be_a_Value_Type()
    {
        var a1 = new SeatingPlace("A", 1, PricingCategory.Second, SeatingPlaceAvailability.Available);
        var a2 = new SeatingPlace("A", 2, PricingCategory.Second, SeatingPlaceAvailability.Available);

        // Two different instances with same values should be equals
        var rowFirstInstance = new Row("A", new List<SeatingPlace> { a1, a2 });
        var rowSecondInstance = new Row("A", new List<SeatingPlace> { a1, a2 });
        Check.That(rowSecondInstance).IsEqualTo(rowFirstInstance);

        // Should not mutate existing instance 
        var a3 = new SeatingPlace("A", 2, PricingCategory.Second, SeatingPlaceAvailability.Available);
        rowSecondInstance.AddSeat(a3);
        Check.That(rowSecondInstance).IsEqualTo(rowFirstInstance);
    }
}
```

You can proceed by testing and refining the `AuditoriumSeating` class to finalize its transformation into a value object. This approach ensures consistency across all related components, aligning with Supple Design principles.

##### AuditoriumSeatingTest.Java

```java
public class AuditoriumSeatingTest {

    @Test
    public void be_a_Value_Type() throws IOException {
        AuditoriumSeatingAdapter auditoriumLayoutAdapter =
                new AuditoriumSeatingAdapter(new AuditoriumLayoutRepository(), new ReservationsProvider());

        String showIdWithoutReservationYet = "18";
        AuditoriumSeating auditoriumSeatingFirstInstance =
                auditoriumLayoutAdapter.getAuditoriumSeating(showIdWithoutReservationYet);
        AuditoriumSeating auditoriumSeatingSecondInstance =
                auditoriumLayoutAdapter.getAuditoriumSeating(showIdWithoutReservationYet);

        // Two different instances with same values should be equals
        assertThat(auditoriumSeatingSecondInstance).isEqualTo(auditoriumSeatingFirstInstance);

        // Should not mutate existing instance
        auditoriumSeatingSecondInstance.rows().values().iterator().next().seats().iterator().next().allocate();
        assertThat(auditoriumSeatingSecondInstance).isEqualTo(auditoriumSeatingFirstInstance);
    }
```

##### AuditoriumSeatingShould .cs

```C#
[TestFixture]
public class AuditoriumSeatingShould
{
    [Test]
    public void Be_a_Value_Type()
    {
        var auditoriumLayoutAdapter =
            new AuditoriumSeatingAdapter(new AuditoriumLayoutRepository(), new ReservationsProvider());
        var showIdWithoutReservationYet = "18";
        var auditoriumSeatingFirstInstance =
            auditoriumLayoutAdapter.GetAuditoriumSeating(showIdWithoutReservationYet);
        var auditoriumSeatingSecondInstance =
            auditoriumLayoutAdapter.GetAuditoriumSeating(showIdWithoutReservationYet);

        // Two different instances with same values should be equals
        Check.That(auditoriumSeatingSecondInstance).IsEqualTo(auditoriumSeatingFirstInstance);

        // Should not mutate existing instance 
        auditoriumSeatingSecondInstance.Rows.Values.First().Seats.First().Allocate();
        Check.That(auditoriumSeatingSecondInstance).IsEqualTo(auditoriumSeatingFirstInstance);
    }
}
```

## Session Four - Deep Modeling

### Deep Modeling Coding

Deep Modeling aims to capture domain complexity in close collaboration with domain experts, discovering deep business rules and accurately representing complex concepts.

#### Steps for Deep Modeling:

**Collaborate Closely with Domain Experts**: Use **Event Storming** or **Collaborative Modeling** sessions to gain a deep understanding of business concepts. Identify critical events, rules, and subdomains.

**Model Complex Business Rules**: Express business rules through Value Objects, Domain Services, or Policy Objects. For instance, if a rule mandates that seats must be reserved in a specific order, create an object that encapsulates this rule and its validations.

**Prototype Critical Rules**: For important rules, such as seating arrangements, build prototypes or tests to validate their behavior before integrating them into the main codebase.

By applying Supple Design and Deep Modeling together, you can create a domain model that is both clear and capable of capturing the real complexity of the business domain. These steps provide a structured approach to managing complexity and ensuring the model aligns with the needs and rules of the domain.

### The middle of the row rule

The rule for placing spectators toward the middle of the row aims to provide a better visual experience and maximize comfort by seating them as close to the center as possible. This rule aims to ensure optimal visual comfort and an improved overall experience for spectators by concentrating seat occupancy around the row’s center.

We propose handling this rule through a prototype to remain as independent as possible from the existing code. You will integrate your new code once it has been tested and is operational.

**Before implementing the middle of the row rule, make sure you have seen its illustration with the domain expert.**

Start with a row of size 10, which corresponds to an even case.

##### In RowTest.java

```java
@Test
public void offer_seats_from_the_middle_of_the_row_when_the_row_size_is_even_and_party_size_is_greater_than_one() {
    int partySize = 2;

    SeatingPlace a1 = new SeatingPlace("A", 1, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a2 = new SeatingPlace("A", 2, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a3 = new SeatingPlace("A", 3, PricingCategory.FIRST, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a4 = new SeatingPlace("A", 4, PricingCategory.FIRST, SeatingPlaceAvailability.RESERVED);
    SeatingPlace a5 = new SeatingPlace("A", 5, PricingCategory.FIRST, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a6 = new SeatingPlace("A", 6, PricingCategory.FIRST, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a7 = new SeatingPlace("A", 7, PricingCategory.FIRST, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a8 = new SeatingPlace("A", 8, PricingCategory.FIRST, SeatingPlaceAvailability.RESERVED);
    SeatingPlace a9 = new SeatingPlace("A", 9, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a10 = new SeatingPlace("A", 10, PricingCategory.SECOND, SeatingPlaceAvailability.AVAILABLE);

    Row row = new Row("A", new ArrayList<>(Arrays.asList(a1, a2, a3, a4, a5, a6, a7, a8, a9, a10)));
    List<SeatingPlace> seatingPlaces = offerSeatsNearerTheMiddleOfTheRow(row).stream().limit(partySize).collect(Collectors.toList());
    assertThat(seatingPlaces).containsExactly(a5, a6);
}

// Deep Modeling: probing the code should start with a prototype.
public List<SeatingPlace> offerSeatsNearerTheMiddleOfTheRow(Row row) {
    // TODO: Implement your logic here
    return new ArrayList<>();
}
```

##### In RowShould.cs

```c#
[Test]
public void Offer_seating_places_from_the_middle_of_the_row_when_the_row_size_is_even_and_party_size_is_greater_than_one()
{
    var partySize = 2;

    var a1 = new SeatingPlace("A", 1, PricingCategory.Second, SeatingPlaceAvailability.Available);
    var a2 = new SeatingPlace("A", 2, PricingCategory.Second, SeatingPlaceAvailability.Available);
    var a3 = new SeatingPlace("A", 3, PricingCategory.First, SeatingPlaceAvailability.Available);
    var a4 = new SeatingPlace("A", 4, PricingCategory.First, SeatingPlaceAvailability.Reserved);
    var a5 = new SeatingPlace("A", 5, PricingCategory.First, SeatingPlaceAvailability.Available);
    var a6 = new SeatingPlace("A", 6, PricingCategory.First, SeatingPlaceAvailability.Available);
    var a7 = new SeatingPlace("A", 7, PricingCategory.First, SeatingPlaceAvailability.Available);
    var a8 = new SeatingPlace("A", 8, PricingCategory.First, SeatingPlaceAvailability.Reserved);
    var a9 = new SeatingPlace("A", 9, PricingCategory.Second, SeatingPlaceAvailability.Available);
    var a10 = new SeatingPlace("A", 10, PricingCategory.Second, SeatingPlaceAvailability.Available);

    var row = new Row("A", new List<SeatingPlace> { a1, a2, a3, a4, a5, a6, a7, a8, a9, a10 });

    var seatingPlaces = OfferSeatsNearerTheMiddleOfTheRow(row).Take(partySize);

    Check.That(seatingPlaces)
        .ContainsExactly(a5, a6);
}

// Deep Modeling: probing the code should start with a prototype.
private IEnumerable<SeatingPlace> OfferSeatsNearerTheMiddleOfTheRow(Row row)
{
    // Deep Modeling: probing the code should start with a prototype.
    return new List<SeatingPlace>();
}
```

You should complete the functionality by testing the case of an odd-sized row.

## Session Five - Refactoring to deeper insights

### The adjacent seats rule

 Seats should be allocated so that each spectator or group of spectators sits next to each other, without an unoccupied seat between them.

The adjacent seat rule in theater seating optimizes occupancy by ensuring groups sit together without gaps, minimizing isolated seats, and favoring placement toward the center of each row for an enhanced spectator experience.

**Before implementing the adjacent seating rule, make sure you have seen its illustration with the domain expert.**

##### In RowTest.java

```java
@Test
public void Offer_adjacent_seats_nearer_the_middle_of_the_row_when_the_middle_is_not_reserved()
{
    int partySize = 3;

    SeatingPlace a1 = new SeatingPlace("A", 1, PricingCategory.Second, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a2 = new SeatingPlace("A", 2, PricingCategory.Second, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a3 = new SeatingPlace("A", 3, PricingCategory.First, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a4 = new SeatingPlace("A", 4, PricingCategory.First, SeatingPlaceAvailability.RESERVED);
    SeatingPlace a5 = new SeatingPlace("A", 5, PricingCategory.First, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a6 = new SeatingPlace("A", 6, PricingCategory.First, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a7 = new SeatingPlace("A", 7, PricingCategory.First, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a8 = new SeatingPlace("A", 8, PricingCategory.First, SeatingPlaceAvailability.RESERVED);
    SeatingPlace a9 = new SeatingPlace("A", 9, PricingCategory.Second, SeatingPlaceAvailability.AVAILABLE);
    SeatingPlace a10 = new SeatingPlace("A", 10, PricingCategory.Second, SeatingPlaceAvailability.AVAILABLE);

    Row row = new Row("A",  new ArrayList<>(Arrays.asList(a1, a2, a3, a4, a5, a6, a7, a8, a9, a10 )));

    List<SeatingPlace> seatingPlaces =
            new OfferingSeatsNearerMiddleOfTheRow(row).offerSeatsNearerTheMiddleOfTheRow(5);
    
    List<SeatingPlace> seatingPlaces = OfferAdjacentSeats(seatsWithDistance, partySize);

    assertThat(seatingPlaces).containsExactly(a5, a6, a7);
}

private List<Seat> OfferAdjacentSeats(List<SeatWithDistance> seatsWithDistance, int partySize) {
    // Implement your prototype here
    return new ArrayList<>();
}
```

##### In RowShould.cs

```c#
[Test]
public void Offer_adjacent_seats_nearer_the_middle_of_the_row_when_the_middle_is_not_reserved()
{
    var partySize = 3;

    var a1 = new SeatingPlace("A", 1, PricingCategory.Second, SeatingPlaceAvailability.Available);
    var a2 = new SeatingPlace("A", 2, PricingCategory.Second, SeatingPlaceAvailability.Available);
    var a3 = new SeatingPlace("A", 3, PricingCategory.First, SeatingPlaceAvailability.Available);

    var a4 = new SeatingPlace("A", 4, PricingCategory.First, SeatingPlaceAvailability.Reserved);

    var a5 = new SeatingPlace("A", 5, PricingCategory.First, SeatingPlaceAvailability.Available);
    var a6 = new SeatingPlace("A", 6, PricingCategory.First, SeatingPlaceAvailability.Available);
    var a7 = new SeatingPlace("A", 7, PricingCategory.First, SeatingPlaceAvailability.Available);

    var a8 = new SeatingPlace("A", 8, PricingCategory.First, SeatingPlaceAvailability.Reserved);
    var a9 = new SeatingPlace("A", 9, PricingCategory.Second, SeatingPlaceAvailability.Available);
    var a10 = new SeatingPlace("A", 10, PricingCategory.Second, SeatingPlaceAvailability.Available);

    var row = new Row("A", new List<SeatingPlace> { a1, a2, a3, a4, a5, a6, a7, a8, a9, a10 });



    var seatingPlaces =
        OfferingSeatsNearerMiddleOfTheRow.OfferSeatsNearerTheMiddleOfTheRow(row);

    var seats =
        OfferAdjacentSeats(seatingPlaces, partySize);

    Check.That(seats).ContainsExactly(a5, a6, a7);
}
// Deep Modeling: probing the code should start with a prototype.
public IEnumerable<SeatingPlace> OfferAdjacentSeats(IEnumerable<SeatingPlace> seatingPlaces, int partySize)
{
    // Deep Modeling: probing the code should start with a prototype.
    return new List<SeatingPlace>();
}
```

**To complete the functionality, you should test it with other row configurations.**
