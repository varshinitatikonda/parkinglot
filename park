import streamlit as st
import time

# Initialize global variables
spaces = []
avail_spaces = 0
total_spaces = 0
rows = 0
space_count = 0
border = ""
linux = 0


class Vehicle:
    def __init__(self, v_type, plate):
        self.type = v_type
        self.plate = plate
        self.entry_time = time.time()

    def get_type_string(self):
        return "Car" if self.type == 1 else "Truck" if self.type == 2 else "Motorcycle"

    def get_plate(self):
        return self.plate

    def get_entry_time(self):
        return self.entry_time

    def set_entry_time(self, new_time):
        self.entry_time = new_time


class Space:
    def __init__(self):
        self.vehicle = None
        self.occupied = False

    def add_vehicle(self, vehicle):
        self.vehicle = vehicle
        self.occupied = True

    def remove_vehicle(self):
        v_exit = self.vehicle
        self.vehicle = None
        self.occupied = False
        return v_exit

    def vehicle_info(self):
        return self.vehicle

    def is_available(self):
        return not self.occupied  # Should return True if not occupied



def initialize_spaces():
    global spaces, avail_spaces, total_spaces, rows, space_count, border
    spaces = [Space() for _ in range(total_spaces)]
    avail_spaces = total_spaces
    space_count = total_spaces // rows


def display_lot():
    global spaces, avail_spaces, total_spaces, rows, space_count
    st.write(f"### SPOTS AVAILABLE: {avail_spaces}")
    for row in range(rows):
        cols = st.columns(space_count)
        for idx, col in enumerate(cols):
            space = spaces[row * space_count + idx]
            if space.is_available():
                col.button("Empty", key=f"{row}-{idx}", disabled=True)
            else:
                vehicle = space.vehicle_info()
                col.button(f"{vehicle.get_type_string()}: {vehicle.get_plate()}", key=f"{row}-{idx}-occupied",
                           disabled=True)


def park_vehicle():
    global spaces, avail_spaces
    st.write("### Park a Vehicle")

    v_type = st.selectbox("Select Vehicle Type", ["Car", "Truck", "Motorcycle"])
    plate = st.text_input("Enter Vehicle Plate Number")
    row = st.number_input("Select Row", min_value=0, max_value=rows - 1, step=1)
    space = st.number_input("Select Space", min_value=0, max_value=space_count - 1, step=1)

    if st.button("Park Vehicle"):
        type_map = {"Car": 1, "Truck": 2, "Motorcycle": 3}
        vehicle = Vehicle(type_map[v_type], plate)
        space_idx = row * space_count + space

        if avail_spaces > 0 and spaces[space_idx].is_available():
            spaces[space_idx].add_vehicle(vehicle)
            avail_spaces -= 1
            st.success("Vehicle Parked Successfully!")
        else:
            st.error("Error: Space is not available or already occupied!")


def exit_vehicle():
    global spaces, avail_spaces
    st.write("### Exit a Vehicle")

    row = st.number_input("Select Row", min_value=0, max_value=rows - 1, step=1)
    space = st.number_input("Select Space", min_value=0, max_value=space_count - 1, step=1)

    space_idx = row * space_count + space
    if st.button("Remove Vehicle"):
        if not spaces[space_idx].is_available():
            vehicle = spaces[space_idx].remove_vehicle()
            avail_spaces += 1
            total_time = time.time() - vehicle.get_entry_time()
            hours = max(1, int(total_time // 3600) + 1)
            rate_map = {1: 3.50, 2: 4.50, 3: 2.00}
            cost = hours * rate_map[vehicle.type]
            st.success(f"Vehicle {vehicle.get_plate()} removed. Total Fare: ${cost:.2f}")
        else:
            st.error("Error: No vehicle in the selected space!")


def view_vehicle():
    global spaces
    st.write("### View a Vehicle")

    row = st.number_input("Select Row", min_value=0, max_value=rows - 1, step=1)
    space = st.number_input("Select Space", min_value=0, max_value=space_count - 1, step=1)

    space_idx = row * space_count + space
    if st.button("View Vehicle"):
        if not spaces[space_idx].is_available():
            vehicle = spaces[space_idx].vehicle_info()
            entry_time = time.strftime('%m-%d-%Y %I:%M %p', time.localtime(vehicle.get_entry_time()))
            st.info(
                f"Vehicle Type: {vehicle.get_type_string()}\nPlate: {vehicle.get_plate()}\nEntry Time: {entry_time}")
        else:
            st.error("Error: No vehicle in the selected space!")

def print_row(row):
    output = "|"
    for s in range(space_count * row, space_count * (row + 1)):
        if spaces[s].is_available():
            output += "[ ]"  # Empty space
        else:
            # Display vehicle type based on the vehicle in the space
            output += "["
            output += "c" if spaces[s].vehicle_info().get_type() == 1 \
                else "t" if spaces[s].vehicle_info().get_type() == 2 \
                else "m"
            output += "]"
        if s < space_count * (row + 1) - 1:
            output += " "
    output += "|"
    return output

def main():
    st.title("Parking Lot Management System")

    global total_spaces, rows

    config_file = st.file_uploader("Upload Config File", type=["txt"])
    if config_file:
        config_lines = config_file.readlines()
        for line in config_lines:
            line = line.decode("utf-8").strip()
            if line.startswith("total_spaces"):
                total_spaces = int(line.split("=")[1].strip())
            elif line.startswith("rows"):
                rows = int(line.split("=")[1].strip())

        initialize_spaces()

        option = st.sidebar.selectbox("Choose an Option",
                                      ["View Parking Lot", "Park a Vehicle", "Exit a Vehicle", "View Vehicle"])
        if option == "View Parking Lot":
            display_lot()
        elif option == "Park a Vehicle":
            park_vehicle()
        elif option == "Exit a Vehicle":
            exit_vehicle()
        elif option == "View Vehicle":
            view_vehicle()


if __name__ == "__main__":
    main()
