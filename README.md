#include <stdio.h>
#include <math.h>

#define GRAVITY 9.81 // m/s^2
#define AIR_DENSITY 1.225 // kg/m^3 at sea level
#define PI 3.141592653589793

// Aircraft parameters
typedef struct {
    double mass;       // kg
    double wing_area;  // m^2
    double drag_coefficient;
    double lift_coefficient;
    double thrust;     // N (Newtons)
} Aircraft;

// State of the aircraft
typedef struct {
    double altitude;  // m
    double speed;     // m/s
    double pitch;     // radians
    double roll;      // radians
    double yaw;       // radians
    double position_x; // m
    double position_y; // m
} AircraftState;

// Function to calculate lift force
double calculate_lift(Aircraft *aircraft, double speed) {
    return 0.5 * AIR_DENSITY * speed * speed * aircraft->wing_area * aircraft->lift_coefficient;
}

// Function to calculate drag force
double calculate_drag(Aircraft *aircraft, double speed) {
    return 0.5 * AIR_DENSITY * speed * speed * aircraft->wing_area * aircraft->drag_coefficient;
}

// Function to calculate thrust (simplified)
double calculate_thrust(Aircraft *aircraft) {
    return aircraft->thrust; // Assuming constant thrust for simplicity
}

// Function to update aircraft position and speed based on forces
void update_aircraft_state(Aircraft *aircraft, AircraftState *state, double delta_time) {
    // Calculate forces
    double lift = calculate_lift(aircraft, state->speed);
    double drag = calculate_drag(aircraft, state->speed);
    double thrust = calculate_thrust(aircraft);

    // Update speed (simplified physics: thrust - drag - weight)
    double net_force = thrust - drag - aircraft->mass * GRAVITY;
    double acceleration = net_force / aircraft->mass;
    state->speed += acceleration * delta_time;

    // Update altitude based on vertical speed (simplified model)
    double vertical_speed = state->speed * sin(state->pitch);
    state->altitude += vertical_speed * delta_time;

    // Update position (horizontal movement)
    state->position_x += state->speed * cos(state->pitch) * delta_time;
    state->position_y += state->speed * sin(state->pitch) * delta_time;
}

// Function to display current state of the aircraft
void display_state(AircraftState *state) {
    printf("Altitude: %.2f m\n", state->altitude);
    printf("Speed: %.2f m/s\n", state->speed);
    printf("Position (X, Y): (%.2f, %.2f)\n", state->position_x, state->position_y);
    printf("Pitch: %.2f degrees\n", state->pitch * 180 / PI);
}

int main() {
    // Initialize aircraft and state
    Aircraft aircraft = {10000, 50, 0.02, 1.2, 15000};  // Example parameters
    AircraftState state = {1000, 200, 0.1, 0.0, 0.0, 0.0, 0.0};  // Initial conditions

    double delta_time = 0.1;  // Time step for simulation (seconds)

    // Simulation loop
    for (int i = 0; i < 1000; i++) {
        // Update the aircraft state
        update_aircraft_state(&aircraft, &state, delta_time);

        // Display the state
        display_state(&state);

        // Wait for user input to simulate control adjustments
        char input;
        printf("Press 'q' to quit, 'u' to increase pitch, 'd' to decrease pitch: ");
        scanf(" %c", &input);

        if (input == 'q') {
            break;
        } else if (input == 'u') {
            state.pitch += 0.05;
        } else if (input == 'd') {
            state.pitch -= 0.05;
        }

        // Sleep for a short time (to simulate real-time simulation)
        usleep(100000);  // 100 ms
    }

    return 0;
}

