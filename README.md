class Seat {

    private String seatNum;
    private String seatClass;
    private boolean isWindow;
    private boolean isAisle;
    private boolean isTable;
    private double seatPrice;
    private String email;

    public Seat(String seatNum, String seatClass, boolean isWindow, boolean isAisle, boolean isTable,
                double seatPrice, String email) {
        this.seatNum = seatNum;
        this.seatClass = seatClass;
        this.isWindow = isWindow;
        this.isAisle = isAisle;
        this.isTable = isTable;
        this.seatPrice = seatPrice;
        this.email = email;
    }

    public String getSeatNum() {
        return seatNum;
    }

    public String getSeatClass() {
        return seatClass;
    }

    public boolean isWindow() {
        return isWindow;
    }

    public boolean isAisle() {
        return isAisle;
    }

    public boolean isTable() {
        return isTable;
    }

    public double getSeatPrice() {
        return seatPrice;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public boolean isReserved() {
        return email != null;
    }

    @Override
    public String toString() {
        return String.format("%s %s %b %b %b %.2f %s",
                seatNum, seatClass, isWindow, isAisle, isTable, seatPrice, email);
    }
}
