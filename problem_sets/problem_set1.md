# Exercise 1

## Problem 1
**Invariants**
1. **Invariant 1:** The count of an item has to be 0 or a positive number.  
2. **Invariant 2:** Every purchase in a registry corresponds to a request in the registry.

The more important invariant is **Invariant 1**. It blocks the ability for the inventory to turn negative and governs what givers can still buy (a major aspect of the system). The `addItem(registry, item, count)` action is most affected and is preserved because our invariant prevents requesters from requesting a negative number of items when adding to the registry.

## Problem 2
The action `purchase(purchaser, registry, item, count)` could break the invariant if the purchaser purchases more items than are remaining in the registry. This could be fixed if our design prevents a purchaser from purchasing more items than the amount currently available in the registry.

## Problem 3
Yes. The requesting party should be allowed to open up the registry for more waves of requesting items. Say they forgot to add an item in the first round of requesting, so they open it a second time in order to request that item.

## Problem 4
This wouldn’t necessarily matter in practice, as it wouldn’t affect the requesting party or any of the functionality related to requesting. The only potential problem would be that the compiling of old registries would lead to storage overflowing or cost issues.

## Problem 5
- **Query 1:** The owner of the registry may want to see what has been purchased from the registry.  
- **Query 2:** The giver of a gift may want to inquire about which items are still available on the registry.

## Problem 6
**State:** You would allow a `hidePurchases` state which can be toggled.  

**Actions:** You would create a `setHidePurchases(registry: Registry, hidePurchases: Flag)` action. It requires the registry to exist and controls whether to hide purchases or not.

Once the registry closes, the owner can see all of the purchases clearly, regardless of whether `hidePurchases` was enabled.

## Problem 7
The **User** and **Item** concepts can be reused across many different retailers and platforms if they don’t have a set structure.

---

# Exercise 2

## Problem 1
- A set of **Users** with:
  - `username: String`  
  - `password: String`
- A set of **Usernames** with:
  - `password: Password`

## Problem 2
- `register(username: String, password: String)`  
  - **requires:** username does not already exist; password is non-empty  
  - **effects:** creates a new user and associates the given password with the username
- `authenticate(username: String, password: String)`  
  - **requires:** the username has already been registered  
  - **effects:** if authentication is successful, the user with `username` will be treated each login as the same user

## Problem 3
For all distinct users, they cannot share the same username. This is preserved by not allowing users to register a username that is already taken.

## Problem 4
**Addition to state:**
- A set of **PendingConfirmations** with:
  - `username: String`  
  - `token: Token`
- A set of **Users** with:
  - `username: String`  
  - `password: String`  
  - `active: Flag`

**Addition to actions:**
- `register(username: String, password: String): (user: User)`  
  - **requires:** username does not already exist; password is non-empty  
  - **effects:** creates a new user with the associated username and password; creates a new pending confirmation associated with the username and a new token.
- `confirm(username: String, token: Token)`  
  - **requires:** a pending confirmation and token exist for said username  
  - **effects:** sets the username to confirmed status and deletes the token.
- `authenticate(username: String, password: String)`  
  - **requires:** the username has already been registered **and confirmed**  
  - **effects:** if authentication is successful, the user with `username` will be treated each login as the same user

**Additions to Invariants:**  
Usernames need to remain unique; only one pending confirmation can exist per username; only usernames that have been confirmed can authenticate.

---

# Exercise 3

**concept** `PersonalAccessToken [User]`  

**purpose** enables access to a user’s GitHub resources without using an account password  

**principle** a user can generate one or more secret tokens with repository scopes and expiration dates. Tokens can also be deleted. The tokens can authenticate users with access to repositories that they are a collaborator on. Tokens are also required for Git over HTTPS.

**state**
- A set of **Users** with:
  - `username: String`
- A set of **Tokens** with:
  - `owner: User`  
  - `secretCode: String`  
  - `scopes: set<Scope>`  
  - `expirationDate: Datetime`  
  - `revoked: Flag`  
  - `lastUsed: Datetime`
- A set of **Authorizations** with:
  - `token: Token`  
  - `organization: String`

**actions**
- `createToken(owner: User, scopes: set<Scope>, expirationDate: Datetime, note?: String): (token: Token)`  
  - **requires:** owner is an existing user; each scope exists  
  - **effects:** creates a new token for the owner with an expiration date, a set of repository scopes, and an optional note.
- `deleteToken(owner: User, token: Token)`  
  - **requires:** token and owner exist  
  - **effects:** removes the token from existence
- `authenticateWithToken(username: String, token: String)`  
  - **requires:** token and username both exist  
  - **effects:** provides the user (with `username`) access to the repositories under the scope of `token`.
- `authorizeToken(token: Token, organization: String)`  
  - **requires:** token exists  
  - **effects:** creates or updates the authorization permissions for `token`

---

# Exercise 4

## Billable Hours Tracking

**concept** `BillableHoursTracking [Employee, Project]`  

**purpose** to capture billable work intervals for each employee and project  

**principle** an employee starts a timed session for a project by entering a description. The session runs until explicitly ended or automatically closed. The total billed hours are computed from each non-overlapping interval of work.

**state**
- A set of **Employees**
- A set of **Projects**
- A set of **Sessions** with:
  - `owner: Employee`  
  - `project: Project`  
  - `description: String`  
  - `startTime: Datetime`  
  - `endTime: Datetime?`  
  - `isActive: Flag`

**actions**
- `startSession(employee: Employee, project: Project, description: String): (session: Session)`  
  - **requires:** employee and project exist  
  - **effects:** creates a new session with `owner = employee`, `project`, `description`, `startTime =` the time of creation, and `isActive = true`
- `endSession(session: Session)`  
  - **requires:** the session exists and is currently active  
  - **effects:** sets the session to inactive (`isActive = false`) and `session.endTime` to the current time
- `editSession(session: Session, startTime?: Datetime, endTime?: Datetime, description?: String)`  
  - **requires:** session exists; `endTime` must succeed `startTime` if both are provided  
  - **effects:** updates the session with the provided fields
- `addManualSession(employee: Employee, project: Project, description: String, startTime: Datetime, endTime: Datetime): (session: Session)`  
  - **requires:** employee and project both exist; `endTime` needs to succeed `startTime`  
  - **effects:** creates a new session with `owner = employee`, `project`, `description`, `startTime`, and `endTime`
- `autocloseSession(maxDuration: Duration)`  
  - **requires:** true  
  - **effects:** for each active session, checks if the runtime of that session (`now - session.startTime`) exceeds `maxDuration`. If so, call `endSession` on that session.

---

## Electronic Boarding Pass

**concept** `ElectronicBoardingPass [Passenger, Flight]`  

**purpose** issues a digital boarding credential that maintains the flight details as they change and allows a passenger to board a designated flight  

**principle** a digital boarding pass is issued to a passenger for a specific flight. The pass contains the flight’s current operational data (gate, time, seat status) and can be presented/scanned once for boarding. Changes are represented in real-time on the pass.

**state**
- A set of **Flight** with:
  - `flightNumber: String`  
  - `departureTime: Datetime`  
  - `departureGate: String`  
  - `status: FlightStatus → {Scheduled | Delayed | Boarding | Closed | Canceled | Departed}`
- A set of **BoardingPass** with:
  - `owner: Passenger`  
  - `flight: Flight`  
  - `seat: String`  
  - `barcode: Barcode`  
  - `status: PassengerStatus → {Issued | Revoked | Boarded}`

**actions**
- `issuePass(passenger: Passenger, flight: Flight, seat: String): (boardingPass: BoardingPass)`  
  - **requires:** passenger, flight, and seat exist; the flight cannot have already departed or been cancelled  
  - **effects:** creates a new boarding pass for the passenger with `flight`, `seat`, and `status = "Issued"`
- `updateFlightInformation(flight: Flight, departureTime?: Datetime, departureGate?: String, status?: FlightStatus)`  
  - **requires:** flight exists  
  - **effects:** updates the provided fields for the flight
- `reassignSeat(boardingPass: BoardingPass, newSeat: String)`  
  - **requires:** boardingPass exists and `newSeat` exists on `boardingPass.flight`  
  - **effects:** sets the boarding pass seat to `newSeat`
- `revokePass(boardingPass: BoardingPass)`  
  - **requires:** boardingPass exists  
  - **effects:** sets the status of `boardingPass` to `"Revoked"`
- `scanPass(boardingPass: BoardingPass): (isAccepted: Boolean)`  
  - **requires:** boardingPass exists; the status of `boardingPass.flight` is either `Boarding`, `Scheduled`, or `Delayed`; the status of `boardingPass` is `Issued`  
  - **effects:** sets the `boardingPass` status to `"Boarded"` and returns `true`

---

## Conference Room Booking

**concept** `ConferenceRoomBooking [User, Room]`  

**purpose** lets users reserve a room for a continuous time interval while preventing double bookings  

**principle** a user requests a room for the beginning of an interval. If the room is free, the system creates a confirmed booking. Users can later modify or cancel their bookings.

**state**
- A set of **Rooms** with:
  - `name: String`  
  - `capacity: Integer`  
  - `features: array<Feature>` *(Note: Features are things like whiteboards, screens, etc.)*  
  - `maxBookingTime: Integer?`
- A set of **Bookings** with:
  - `owner: User`  
  - `room: Room`  
  - `startTime: Datetime`  
  - `endTime: Datetime`  
  - `purpose: String?`  
  - `attendees: Integer?`  
  - `status: Status`
- A set of **Blocks** *(reserved time slots)* with:
  - `admin: User`  
  - `room: Room`  
  - `startTime: Datetime`  
  - `endTime: Datetime`  
  - `reasoning: String?`

**actions**
- `createBooking(user: User, room: Room, startTime: Datetime, endTime: Datetime, purpose?: String, attendees?: Integer): (booking: Booking)`  
  - **requires:** the room exists; `endTime` succeeds `startTime`; the time span between `startTime` and `endTime` must be less than `room.maxBookingTime` if it exists.  
    No other bookings can be scheduled in the interval between `startTime` and `endTime`.  
    No other blocks can be scheduled in the interval between `startTime` and `endTime`.  
  - **effects:** creates a new booking with the given fields.
- `cancelBooking(user: User, booking: Booking)`  
  - **requires:** the booking exists, and `booking.status` is `"Confirmed"`  
  - **effects:** sets the status of `booking` to `"Canceled"`
- `modifyBooking(user: User, booking: Booking, newStart?: Datetime, newEnd?: Datetime, newPurpose?: String, newAttendees?: Integer)`  
  - **requires:** booking exists and the status of booking is `"Confirmed"`; user must also be the owner of the room booking (`booking.owner = user`).  
    `newEnd` must succeed `newStart` if both are provided.  
    If `newStart` is provided and `newEnd` is not, no other bookings or time blocks can exist between `newStart` and `booking.endTime` or `block.endTime`.  
    If `newStart` is not provided and `newEnd` is, no other bookings or time blocks can exist between `booking.startTime` or `block.startTime` and `endTime`.  
    If `newStart` and `newEnd` are both provided, no booking or time block can exist between the interval of `newStart` and `newEnd`.  
  - **effects:** updates the provided fields of `booking`.
- `completePastBookings()`  
  - **requires:** true  
  - **effects:** sets the status of every booking to `"Complete"` if that booking has the status of `"Confirmed"` and the current time succeeds the end time of the booking.
- `createBlock(admin: User, room: Room, startTime: Datetime, endTime: Datetime, reason?: String): (block: Block)`  
  - **requires:** admin has admin-level access; `endTime` succeeds `startTime`  
  - **effects:** creates a new block with the provided fields.
- `removeBlock(admin: User, block: Block)`  
  - **requires:** admin has admin-level roles; `admin = block.admin`; block exists  
  - **effects:** deletes the block
