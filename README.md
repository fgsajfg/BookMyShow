public enum Language {
    HINDI, ENGLISH;
}
public enum Genre {
    ACTION, ROMANCE, COMEDY, HORROR;
}
public class Movie {
    private String name;
    private float ratings = 0.0f;
    private Language language;
    private Genre genre;

    public Movie(String name, Language language, Genre genre) {
        this.name = name;
        this.language = language;
        this.genre = genre;
    }

    public String getName() {
        return name;
    }

    public float getRatings() {
        return ratings;
    }

    public Language getLanguage() {
        return language;
    }

    public Genre getGenre() {
        return genre;
    }
}

import java.util.ArrayList;

public class Theater {
    private static int idCounter=0;
    private int id;
    private String name;
    private String location;
    private int capacity;
    private ArrayList<Show> shows;

    public Theater(String name, String location, int capacity) {
        idCounter += 1;
        this.id = idCounter;
        this.name = name;
        this.location = location;
        this.capacity = capacity;
        this.shows = new ArrayList<>();
    }

    public void updateShow(Show oldShow, Show newShow){

    }
    public ArrayList<Show> getShows(){
        return shows;
    }

    public String getName() {
        return name;
    }

    public int getCapacity() {
        return capacity;
    }
}

import java.util.Date;

public class Show {
    private static int idCounter=0;
    private int id;
    private Date showTime;
    private Movie movie;
    private Theater theater;
    private int availableSeats;

    public Show(Date showTime, Movie movie, Theater theater) {
        idCounter += 1;
        this.id = idCounter;
        this.showTime = showTime;
        this.movie = movie;
        this.theater = theater;
        this.availableSeats = theater.getCapacity();
        theater.getShows().add(this);
    }

    public Movie getMovie() {
        return movie;
    }
    public void setTheater(Theater theater) {
        this.theater = theater;
    }
    public void setAvailableSeats(int availableSeats) {
        this.availableSeats = availableSeats;
    }
    public int getAvailableSeats() {
        return availableSeats;
    }
    public void updateShow(){
    }
    public synchronized Ticket bookTicket(RegisteredUser user, int seats){
        if(availableSeats >= seats && seats >0){
            Ticket ticket = new Ticket();
            availableSeats -= seats;
            ticket.setOwner(user.getName());
            ticket.setBookedShow(this);
            ticket.setBookingTime(new Date());
            ticket.setNumberOfSeats(seats);
            System.out.println("Successfully booked");
            user.bookingHistory.add(ticket);
            return ticket;
        }
        else{
            System.out.println("Seats not Available");
            return null;
        }
    }
    @Override
    public String toString() {
        return "Show{" +
                "id=" + id +
                ", showTime=" + showTime +
                ", movie=" + movie.getName() +
                ", theater=" + theater.getName() +
                ", availableSeats=" + availableSeats +
                '}';
    }
}

import java.util.Date;

public class Ticket {
    private static int idCounter=0;
    private int id;
    private String owner;
    private Date bookingTime;
    private int numberOfSeats;
    private Show bookedShow;

    public Ticket() {
        idCounter += 1;
        this.id = idCounter;
    }

    public String getTicketInfo(){
        return null;
    }
    public int cancelTicket(){
        return 0;
    }

    public String getOwner() {
        return owner;
    }

    public void setOwner(String owner) {
        this.owner = owner;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public Date getBookingTime() {
        return bookingTime;
    }

    public void setBookingTime(Date bookingTime) {
        this.bookingTime = bookingTime;
    }

    public int getNumberOfSeats() {
        return numberOfSeats;
    }

    public void setNumberOfSeats(int numberOfSeats) {
        this.numberOfSeats = numberOfSeats;
    }

    @Override
    public String toString() {
        return "Ticket{" +
                "owner='" + owner + ''' +
                ", bookingTime=" + bookingTime +
                ", numberOfSeats=" + numberOfSeats +
                ", bookedShow=" + bookedShow +
                '}';
    }

    public Show getBookedShow() {
        return bookedShow;
    }

    public void setBookedShow(Show bookedShow) {
        this.bookedShow = bookedShow;
    }
}

public abstract class User {
    private static int idCounter=0;
    private int id;
    private String name;

    public User(String name) {
        idCounter += 1;
        this.id = idCounter;
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

public class GuestUser extends User {
    public GuestUser(String name) {
        super(name);
    }

    public void register(String username, String password){

    }
}

import java.util.ArrayList;

public class RegisteredUser extends User {
    public ArrayList<Ticket> bookingHistory;

    public RegisteredUser(String name) {
        super(name);
        this.bookingHistory = new ArrayList<>();
    }
    public void cancelTicket(Ticket ticket){

    } 
}

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;

public class BookMyShow {
    ArrayList<Theater> theaters;
    static HashMap<String,ArrayList<Show>> movieMap;

    private void generateMovieMap(){
        for (Theater theater :theaters) {
            ArrayList<Show> showArray = theater.getShows();
            for(Show show : showArray) {
                if (show != null) {
                    if (movieMap.containsKey(show.getMovie().getName())) {
                        movieMap.get(show.getMovie().getName()).add(show);
                    } else {
                        ArrayList<Show> movieShowList = new ArrayList<>();
                        movieShowList.add(show);
                        movieMap.put(show.getMovie().getName(), movieShowList);
                    }
                }
            }
        }
    }
    public BookMyShow(ArrayList<Theater> theaters) {
        this.theaters = theaters;
        this.movieMap = new HashMap<>();
        generateMovieMap();
        System.out.println(movieMap);
    }
    public static ArrayList<Show> searchShows(String movieName){
        if (movieMap.containsKey(movieName)){
            return movieMap.get(movieName);
        }
        else
            return null;
    }

    public static void main(String[] args) {
        /* --------Data generation code ----START ----------------- */

        // Creating Guest User --> Piyush
        GuestUser piyush = new GuestUser("Piyush");

        // Creating Registered User --> Ayush
        RegisteredUser ayush = new RegisteredUser("Ayush");

        // Creating Registered User --> Saurabh
        RegisteredUser saurabh = new RegisteredUser("Saurabh");

        // Creating Movie object --> Iron Man
        Movie ironMan = new Movie("Iron Man", Language.ENGLISH,Genre.ACTION);

        // Creating Movie object --> Avengers: End Game
        Movie avengers = new Movie("Avengers: End Game", Language.ENGLISH,Genre.ACTION);

        // Creating Movie object --> The Walk To Remember
        Movie walkToRemember = new Movie("The Walk To Remember", Language.ENGLISH,Genre.ROMANCE);

        // Creating Movie object --> HouseFull2
        Movie housefull = new Movie("HouseFull 2", Language.HINDI,Genre.COMEDY);

        // Creating Theater --> PVR @ GIP Noida with capacity 30
        Theater pvr_gip = new Theater("PVR","GIP Noida",30);

        // Creating Another Theater --> BIG Cinema @ Noida Sector 137 with capacity 40
        Theater big_cinema = new Theater("Big Cinema","Sector 137 Noida",40);




        // Creating four shows for movies
        Show show1=null, show2=null, show3=null, show4=null;
        SimpleDateFormat formatter = new SimpleDateFormat("EEEE, MMM dd, yyyy HH:mm:ss a");

        try {
            // Creating Show for Movie Iron Man on 7 Jun 2020 @ 9:00 AM in PVR
            String dateInString = "Friday, Jun 7, 2020 09:00:00 AM";
            Date date = formatter.parse(dateInString);
            show1 = new Show(date,ironMan,pvr_gip);

            // Creating Show for Movie HOUSEFULL on 7 Jun 2020 @ 12:00 PM in PVR
            dateInString = "Friday, Jun 7, 2020 12:00:00 PM";
            date = formatter.parse(dateInString);
            show2 = new Show(date,housefull,pvr_gip);

            // Creating Show for Movie WALK TO REMEMBER on 7 Jun 2020 @ 09:00 AM in BIG-CINEMA
            dateInString = "Friday, Jun 7, 2020 09:00:00 AM";
            date = formatter.parse(dateInString);
            show3 = new Show(date,walkToRemember,big_cinema);

            // Creating Show for Movie WALK TO REMEMBER on 7 Jun 2020 @ 12:00 PM in BIG-CINEMA
            dateInString = "Friday, Jun 7, 2020 12:00:00 PM";
            date = formatter.parse(dateInString);
            show4 = new Show(date,walkToRemember,big_cinema);

            } catch (ParseException e) {
                    e.printStackTrace();
        }

        /* --------Data generation code ---- END ----------------- */

        // Now We have two theaters with their shows, lets add these theaters to our Book My Show app
        ArrayList<Theater> theaterArrayList= new ArrayList<>();
        theaterArrayList.add(pvr_gip);
        theaterArrayList.add(big_cinema);
        BookMyShow bookMyShow = new BookMyShow(theaterArrayList);

        // Searching Book My Show for all the shows of movie WALK TO REMEMBER
        ArrayList<Show> searchedShow = BookMyShow.searchShows("The Walk To Remember");
        // Above code returns two shows of WALK TO REMEMBER
        /* 
        searchedShow --> [Show{id=3, showTime=Sun Jun 07 09:00:00 IST 2020, 
        movie=The Walk To Remember, theater=Big Cinema, availableSeats=40}, 
        Show{id=4, showTime=Sun Jun 07 12:00:00 IST 2020, movie=The Walk To Remember, 
        theater=Big Cinema, availableSeats=40}]
        */
        // Now suppose AYUSH and SAURABH both wants to book 10 tickets each for first show
        // Then Book My show will create two Ticket Booking threads for their requests

        Show bookingShow = searchedShow.get(0);

        // Ticket Booking Thread for the request made by AYUSH for 10 Seats
        TicketBookingThread t1 = new TicketBookingThread(bookingShow,ayush,10);

        // Ticket Booking Thread for the request made by SAURABH for 10 Seats
        TicketBookingThread t2 = new TicketBookingThread(bookingShow,saurabh,10);

       // Start both the Ticket Booking Threads for execution
        t1.start();
        t2.start();

        // Waiting till both the thread completes the execution
        try {
            t1.join();
            t2.join();

        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // After execution t1 will carry AYUSH ticket and t2 will carry SAURABH ticket
        Ticket ayush_ticket = t1.getTicket();
        Ticket saurabh_ticket = t2.getTicket();

        // Printing their tickets
        System.out.println(t1.getTicket());
        System.out.println(t2.getTicket());

        // Now, 20 seats are booked for this show and 20 seats are available,
        // Suppose AYUSH wants another 15 seats and SAURABH wants another 10 seats to be booked

        // Ticket Booking Thread for the request made by AYUSH for another 15 Seats
        TicketBookingThread t3 = new TicketBookingThread(bookingShow,ayush,15);

        // Ticket Booking Thread for the request made by SAURABH for another 10 Seats
        TicketBookingThread t4 = new TicketBookingThread(bookingShow,saurabh,10);

        // Start both the Ticket Booking Threads for execution
        t3.start();
        t4.start();

        // Waiting till both the thread completes the execution
        try {

            t4.join();
            t3.join();

        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // After execution t3 will carry AYUSH ticket and t4 will carry SAURABH ticket
        Ticket ayushNewTicket = t3.getTicket();
        Ticket saurabhNewTicket = t4.getTicket();

        System.out.println(ayushNewTicket);
        System.out.println(saurabhNewTicket);

        /* Running this program several times, we will notice that 
        if t3 enters the bookTicket function first, 
        then ticket is allocated to Ayush, 
        otherwise ticket is allocated to Saurabh.
         */

    }
}

public class TicketBookingThread extends Thread {
    private Show show;
    private RegisteredUser user;
    private int numberOfSeats;
    private Ticket ticket;

    public TicketBookingThread(Show show, RegisteredUser user, int numberOfSeats) {
        this.show = show;
        this.user = user;
        this.numberOfSeats = numberOfSeats;
    }

    @Override
    public void run() {
        this.ticket = show.bookTicket(user,numberOfSeats);
    }

    public Ticket getTicket() {
        return ticket;
    }
}
