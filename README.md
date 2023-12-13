




import java.io.BufferedReader;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class OrderingSystem {
    private static Scanner input = new Scanner(System.in);
    private static SeatBookingSystem bookingSystem;
    public static String fileName = "C:/Users/erfan/Downloads/seats.txt";

    public static void main(String[] args) {
        try {
            // Load seat data from the file
            Seat[] seatsArray = readSeatsFromFile(fileName);
            bookingSystem = new SeatBookingSystem(seatsArray);
            String choice;
            do {
                System.out.println("\n--MAIN MENU--");
                System.out.println("1- Display Menu");
                System.out.println("2- Reserve Seat ");
                System.out.println("3- Cancel Seat");
                System.out.println("Q- Quit");
                System.out.println("Pick :");
                choice = input.nextLine().toUpperCase();

                switch (choice) {
                    case "1": {
                        displaySeats();
                        break;
                    }
                    case "2": {
                        reserveSeat();
                        break;
                    }
                    case "3": {
                        cancelSeat();
                        break;
                    }
                }
            } while (!choice.equals("Q"));
            System.out.println("--GoodBye--");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Seat[] readSeatsFromFile(String filePath) throws IOException {
        List<String> lines = new ArrayList<>();

        try (BufferedReader bufferedReader = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = bufferedReader.readLine()) != null) {
                lines.add(line);
            }
        } catch (IOException e) {
            System.err.println("Error reading from the file.");
            e.printStackTrace();
        }

        int seatCount = lines.size();
        Seat[] seats = new Seat[seatCount];

        for (int i = 0; i < seatCount; i++) {
            String[] seatDetails = lines.get(i).split("\\s+");
            String classType = seatDetails[0];
            String seatNumber = seatDetails[1];

            boolean isWindow = "true".equalsIgnoreCase(seatDetails[2]);
            boolean isAisle = "true".equalsIgnoreCase(seatDetails[3]);
            boolean isTable = "true".equalsIgnoreCase(seatDetails[4]);
            double seatPrice = Double.parseDouble(seatDetails[5]);
            String reservationEmail = seatDetails[6];

            seats[i] = new Seat(seatNumber, classType, isWindow, isAisle, isTable, (float) seatPrice, reservationEmail);
        }

        return seats;
    }

    private static void displaySeats() {
        System.out.println("--DISPLAY MENU--");

        List<Seat> allSeats = bookingSystem.getAllSeats();
        for (Seat seat : allSeats) {
            System.out.println(seat.toString());
        }
    }

    private static void reserveSeat() {
        System.out.println("--RESERVE SEAT--");
        System.out.print("Enter your email: ");
        String email = input.nextLine();
        System.out.print("Enter the maximum price you can afford: ");
        double maxPrice = input.nextDouble();
        input.nextLine(); // Consume the newline character

        // Get available seats based on user input
        List<Seat> availableSeats = bookingSystem.getAvailableSeats(email, maxPrice);

        if (availableSeats.isEmpty()) {
            System.out.println("No available seats matching your criteria.");
        } else {
            System.out.println("Available Seats:");
            for (Seat seat : availableSeats) {
                System.out.println(seat.toString());
            }

            System.out.print("Enter the seat number to reserve: ");
            String seatNum = input.nextLine();

            // Reserve the selected seat
            boolean success = bookingSystem.reserveSeat(seatNum, email);
            if (success) {
                System.out.println("Seat reserved successfully.");
            } else {
                System.out.println("Failed to reserve the seat. Please try again.");
            }
        }
    }

    private static void cancelSeat() {
        System.out.println("--CANCEL SEAT--");
        System.out.print("Enter your email: ");
        String email = input.nextLine();

        System.out.print("Enter the seat number to cancel reservation: ");
        String seatNum = input.nextLine();

        // Cancel the seat reservation
        boolean success = bookingSystem.cancelSeat(seatNum, email);
        if (success) {
            System.out.println("Seat reservation canceled successfully.");
        } else {
            System.out.println("Failed to cancel the seat reservation. Please try again.");
        }
    }
}

class SeatBookingSystem {
    private List<Seat> seats;

    public SeatBookingSystem(Seat[] seatsArray) {
        seats = new ArrayList<>();
        for (Seat seat : seatsArray) {
            this.seats.add(seat);
        }
    }

    public List<Seat> getAllSeats() {
        // Return all seats
        return seats;
    }

    public List<Seat> getAvailableSeats(String email, double maxPrice) {
        List<Seat> availableSeats = new ArrayList<>();
        for (Seat seat : seats) {
            if (!seat.isReserved() && seat.getSeatPrice() <= maxPrice) {
                availableSeats.add(seat);
            }
        }
        return availableSeats;
    }

    public boolean reserveSeat(String seatNum, String email) {
        Seat seat = findSeatByNumber(seatNum);
        if (seat != null && !seat.isReserved()) {
            seat.setEmail(email);
            return true;
        }
        return false;
    }

    public boolean cancelSeat(String seatNum, String email) {
        Seat seat = findSeatByNumber(seatNum);
        if (seat != null && seat.isReserved() && seat.getEmail().equals(email)) {
            seat.setEmail(null);
            return true;
        }
        return false;
    }

    private Seat findSeatByNumber(String seatNum) {
        for (Seat seat : seats) {
            if (seat.getSeatNum().equals(seatNum)) {
                return seat;
            }
        }
        return null;
    }
}
