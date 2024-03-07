# flight-booking-system
#python code for flight booking system project

from collections import Counter
import pandas as pd
from datetime import datetime, date

admins = [{"username": "Vishal", "password": 123}, {"username": "Sudheer", "password": 123},
          {"username": "Manikanta", "password": 123}, {"username": "Dhiyanesh", "password": 123},
          {"username": "Revanth", "password": 123}]

users = []
flights = []
records = []

flightDetails = pd.DataFrame([])
bookingDetails = pd.DataFrame([])
booked_flights = []


class Flight:
    def __init__(self, flight_number, airlines, source, destination, capacity):
        self.number = flight_number
        self.airlines = airlines
        self.total_capacity = capacity
        self.available_capacity = [i for i in range(1, capacity + 1)]
        self.source = source
        self.destination = destination
        self.add_flight_details()

    def add_flight_details(self):
        global flightDetails
        flightDetails = flightDetails.append(
            {"Number": self.number, "Airlines": self.airlines, "Capacity": self.total_capacity,
             "Source": self.source,
             "Destination": self.destination}, ignore_index=True)


code = {"Indigo": "IND", "Air India": "ai", "Vistara": "VST", "Arab Emirates": "ARM"}
x = 1


def create_flights(a, s, d, c):
    global x
    for i in range(25):
        flight_number = str(x + i) + code[a]
        airlines = a
        source = s
        destination = d
        capacity = c
        flights.append(Flight(flight_number, airlines, source, destination, capacity))


create_flights("Indigo", "chennai", "hyderabad", 150)
create_flights("Air India", "Bangalore", "hyderabad", 200)
create_flights("Vistara", "Mumbai", "Gujarat", 200)
create_flights("Arab Emirates", "Kolkata", "Delhi", 150)

Cities = []
Dest = []
for i in flights:
    Cities.append(i.source)
    Dest.append(i.destination)


class Records:
    def __init__(self, name, passenger_id, booking_id, seat_number, flight_number, date):
        self.name = name.title()
        self.passenger_id = passenger_id
        self.booking_id = booking_id
        self.seat_no = seat_number
        self.flight_no = flight_number
        self.flight_date = date
        self.add_booking_details()
        self.add_booking_confirmation()

    def add_booking_details(self):
        global bookingDetails
        bookingDetails = bookingDetails.append({"UserName": self.name,
                                                "booking_id": self.booking_id,
                                                "passenger_id": self.passenger_id,
                                                "Journey Date": self.flight_date,
                                                "seat number": self.seat_no,
                                                "flight number": self.flight_no}, ignore_index=True)

    def add_booking_confirmation(self):
        global booked_flights
        booked_flights.append([self.name, self.passenger_id])

    def change_itinerary(self, c1, ch):
        if ch == "name":
            self.name = c1.title()
        elif ch == "flight_date":
            self.flight_date = c1


def check_date_available(d):
    d = datetime.strptime(d, "%Y-%m-%d").date()
    if d >= date.today():
        return True
    else:
        return False


def admin_authentication(username, password):
    for admin in admins:
        if admin["username"] == username and admin["password"] == password:
            return True
    return False


def user_authentication(username, password):
    for user in users:
        if user["username"] == username and user["password"] == password:
            return True
    return False


temp = True
while temp:
    print("1. Admin\n2. User")
    user_type = int(input("Are you an admin or a user: "))
    print("1. Sign up\n2. Sign in")
    sign_method = int(input("Sign in or Sign up: "))
    username, password = input("Enter username: "), int(input("Enter password: "))

    if user_type == 1:
        if sign_method == 1:
            if admin_authentication(username, password):
                print("Admin already present. Try signing in.")
                continue
            else:
                new_admin = {"username": username, "password": password}
                admins.append(new_admin)
                print("Admin created successfully.")
        else:
            if admin_authentication(username, password):
                print("You are successfully logged in as an admin.")
            else:
                print("Admin not found. Try signing up first.")
                continue

        print(f"Welcome {username} --admin")
        admin_option = int(input("1. Flight occupancy rates\n2. Popular Routes\n3. Passenger booking statistics: "))

        if admin_option == 1:
            Occupancy_Rate_Choice = int(input("1. Occupancy rate by Flight Number\n2. Show Occupancy rates of all flights"))
            if Occupancy_Rate_Choice == 1:
                Occu_Flight = input("Please Enter the Flight Number: ")
                for Flights in flights:
                    if Occu_Flight == Flights.number:
                        Cons = Flights.total_capacity - len(Flights.available_capacity)
                        Cons1 = Cons / Flights.total_capacity
                        print(f"The occupancy rate of {Flights.number} is {Cons1 * 100}")

            elif Occupancy_Rate_Choice == 2:
                for Flights in flights:
                    Cons = Flights.total_capacity - len(Flights.available_capacity)
                    Cons1 = Cons / Flights.total_capacity
                    print(f"The occupancy rate of {Flights.number} is {Cons1 * 100}")

        elif admin_option == 2:
            def famous_routes(flights, num_routes):
                routes = Counter((flight.source, flight.destination) for flight in flights)
                famous_routes = routes.most_common(num_routes)
                return famous_routes

            num_routes = int(input("Enter the number of famous routes to display: "))
            fam_routes = famous_routes(flights, num_routes)
            print("Famous Routes:")
            for (source, destination), count in fam_routes:
                print(f"Route: {source} to {destination}, Count: {count}")

        elif admin_option == 3:
            Mode_Of_Statistics = int(input("1.See Customer booking frequency\n2.Show all customer booking records\n3.Go back"))
            if Mode_Of_Statistics == 1:
                c = 0
                ll1 = []
                for i in booked_flights:
                    for j in booked_flights:
                        if i[0] == j[0]:
                            c += 1
                    ll1.append([i[0], c])
                    c = 0
                ll2 = []
                for i in ll1:
                    if i not in ll2:
                        ll2.append(i)
                # print(ll2)
                ll3 = sorted(ll2, key=lambda x: x[0], reverse=True)
                print("Customer Name    Bookings")
              
                for i in ll3:
                    print(f"{i[0]}       {i[1]}")

            elif Mode_Of_Statistics == 2:
                print(bookingDetails.to_string(index=False))


    else:
        if sign_method == 1:
            if user_authentication(username, password):
                print("User already present. Try signing in.")
                continue
            else:
                new_user = {"username": username, "password": password}
                users.append(new_user)
                print("Signed up successfully.")
        else:
            if user_authentication(username, password):
                print("Logged in successfully.")
            else:
                print("User not found. Try signing up.")
                continue

        print(f"Hey {username} --user")
        print("1. Search Flights in Between\n2. Select a Flight\n3. Modify/Show Booking Details")

        user_option = int(input("Service: "))

        if user_option == 1:
            source_ap = input("Enter the source airport code: ")
            destination_ap = input("Enter the destination airport code: ")
            if source_ap not in Cities or destination_ap not in Dest:
                print("Cities not found.")
                continue
            flight_date = input("Enter date of booking (YYYY-MM-DD): ")
            if check_date_available(flight_date):
                flight_details = flightDetails[(flightDetails["Source"] == source_ap) &
                                               (flightDetails["Destination"] == destination_ap)]
                print(flight_details.to_string(index=False))
                print("1. Select a flight\n2. Back to menu")
                flight_details_option = int(input())

                if flight_details_option == 1:
                    flight_number = input("Enter flight number: ")
                    for flight in flights:
                        if flight.number == flight_number:
                            print(flight.total_capacity)
                            print(flight.available_capacity)
                            no_of_seats = int(input("No of seats to be booked: "))
                            seats = [input("Enter seats:") for _ in range(no_of_seats)]
                            cust_name = input("Enter your name: ")

                            booking_id = str(datetime.now()).split(".")[1]
                            print(f"Seat number {','.join([str(i) for i in seats])} has been booked on airline {flight.airlines} with flight number {flight.number}")
                            for seat in seats:
                                booking = Records(cust_name, cust_name[::-1][1:2] + booking_id[::-1][0], booking_id, seat, flight_number, flight_date)
                                records.append(booking)
                                booking = None
                            for seat in seats:
                                flight.available_capacity.remove(int(seat))
                            seats = []

            else:
                print("Flight not available for the entered date.")

        elif user_option == 2:
            flight_number = int(input("Please enter flight number: "))
            flight_date = input("Please enter date of booking (YYYY-MM-DD): ")
            if check_date_available(flight_date):
                for flight in flights:
                    if flight.number == flight_number:
                        print(flight.total_capacity)
                        no_of_seats = int(input("No of seats to be booked: "))
                        seats = [input("Enter seats:") for _ in range(no_of_seats)]
                        customer_name = input("Enter your name: ")
                        customer_id = input("Enter passenger id: ")
                        print(f"Seat number {','.join(seats)} has been booked on airline {flight.airlines} with flight number {flight.number}")
                        for seat in seats:
                            record = Records(customer_name, customer_id, customer_name[::-1][1:2] + customer_id[::-1][0], seat, flight_number, flight_date)
                            records.append(record)
                            record = None
                        for seat in seats:
                            flight.available_capacity.remove(int(seat))
                        seats = []
            else:
                print("Date has passed.")

        elif user_option == 3:
            print("1. Show bookings\n2. Cancel a booking\n3. Change flight itinerary\n4. Go back")
            ip = int(input("Please enter the modification to be done: "))
            if ip == 1:
                username = input("Please enter the username: ")
                details_by_name = bookingDetails.loc[bookingDetails["UserName"] == username.title()]
                print(details_by_name.to_string(index=False))
            elif ip == 2:
                Flight_No = input("Please enter the Flight number: ")
                Seat = int(input("Please Enter the seat number: "))
                bookingDetails = bookingDetails.drop(bookingDetails[(bookingDetails["flight number"] == Flight_No) & (bookingDetails["seat number"] == Seat)].index)
                for Flights in flights:
                    if Flights.number == Flight_No:
                        Flights.available_capacity.append(Seat)
                print("Booking has been successfully cancelled")      
            elif ip == 3:
                B_id=int(input("Please enter the Booking_id"))
                Mod=int(input("1.Change Name of booking\n2.Change date of journey"))
                if Mod==1:
                    N_Name=input("Please enter the updated name: ")
                    for i in records :
                        if i.booking_id==B_id:
                            i.change_itinerary(N_Name,"name")
                    print("Changes have been made.")
                elif Mod==2:
                    N_Date=input("Please enter the updated date: ")
                    for i in records :
                        if i.booking_id==B_id:
                            i.change_itinerary(N_Date,"flight_date")
                    print("Changes have been made.")

            elif ip == 4:
                continue

        else:
            break
