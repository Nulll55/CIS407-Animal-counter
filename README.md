# CIS407-Animal-counter

// Countable.java
public interface Countable {
    void incrementCount();
}


// Animal.java
public abstract class Animal {
    protected int count;

    public void resetCount() {
        this.count = 0;
    }

    public int getCount() {
        return this.count;
    }
}


// Alligator.java
import javax.swing.JOptionPane;

public class Alligator extends Animal implements Countable {
    // Reference to the Sheep object is needed for deduction/checking
    private Sheep sheep;

    // Constructor initializes count to zero and sets the sheep reference
    public Alligator(Sheep sheep) {
        this.count = 0;
        this.sheep = sheep;
    }

    // Override the incrementCount() method
    @Override
    public void incrementCount() {
        this.count += 1; // Add 1 more alligator

        // Deduct 1 sheep if any are left
        if (sheep.getCount() > 0) {
            // Note: We access the 'count' of sheep here, assuming Sheep is accessible
            // (or we could use a public 'decrement' method in Sheep)
            sheep.decrementCount(1); 
        }

        // Display message if Alligators exceed Sheep
        if (this.count > sheep.getCount()) {
            JOptionPane.showMessageDialog(null, 
                "Please add more sheep for the hungry alligators", 
                "Warning: Low Sheep!", JOptionPane.WARNING_MESSAGE);
        }
    }
}

// Sheep.java
import javax.swing.JOptionPane;

public class Sheep extends Animal implements Countable {
    // Reference to the Alligator object is needed for checking safety message
    private Alligator alligator;

    // Constructor initializes count to zero and sets the alligator reference
    public Sheep(Alligator alligator) {
        this.count = 0;
        this.alligator = alligator;
    }

    // Helper method for Alligator to deduct sheep
    public void decrementCount(int amount) {
        this.count = Math.max(0, this.count - amount); // Ensure count doesn't go below zero
    }

    // Override the incrementCount() method
    @Override
    public void incrementCount() {
        this.count += 2; // Add 2 more sheep

        // Display message if there are no alligators
        if (alligator.getCount() == 0) {
            JOptionPane.showMessageDialog(null, 
                "No alligators now so the sheep are safe", 
                "Safety First!", JOptionPane.INFORMATION_MESSAGE);
        }
    }
}


----animal count gui -------
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class AnimalCounterGUI extends JFrame {
    // a. Objects for counting
    private Alligator alligator;
    private Sheep sheep;

    // b. GUI Components
    private JLabel welcomeLabel; // a.
    private JLabel alligatorCountLabel; // b.
    private JLabel sheepCountLabel; // c.
    private JButton addAlligatorButton; // d.
    private JButton addSheepButton; // e.
    private JButton displayCountsButton; // f.
    private JButton resetCountsButton; // g.
    private JButton exitButton; // i.
    private JRadioButton alligatorRadioButton; // h.
    private JRadioButton sheepRadioButton; // h.
    private ButtonGroup radioGroup; // For mutual exclusion of radio buttons

    public AnimalCounterGUI() {
        // Initialize animal objects (must be done carefully due to circular dependency)
        // We create the objects first, then link them.
        alligator = new Alligator(null); 
        sheep = new Sheep(alligator);
        // Now update Alligator's sheep reference:
        alligator = new Alligator(sheep); 

        initializeComponents();
        setupLayout();
        registerListeners();
        
        // d. Initialize and display counts
        updateCountLabels(); 
    }

    private void initializeComponents() {
        // Welcome Message (Step 3a)
        welcomeLabel = new JLabel("Welcome to the Animal Counter", SwingConstants.CENTER);
        welcomeLabel.setFont(new Font("SansSerif", Font.BOLD, 16));

        // Count Labels (Step 3b, 3c)
        alligatorCountLabel = new JLabel("Alligators: 0", SwingConstants.LEFT);
        sheepCountLabel = new JLabel("Sheep: 0", SwingConstants.LEFT);
        
        // Buttons (Step 3d, 3e, 3f, 3g, 3i)
        addAlligatorButton = new JButton("Add Alligator (+1)");
        addSheepButton = new JButton("Add Sheep (+2)");
        displayCountsButton = new JButton("Display Counts");
        resetCountsButton = new JButton("Reset Counts");
        exitButton = new JButton("Exit");

        // Radio Buttons (Step 3h)
        alligatorRadioButton = new JRadioButton("Alligator");
        sheepRadioButton = new JRadioButton("Sheep");
        alligatorRadioButton.setSelected(true); // Default selection

        // Group the radio buttons
        radioGroup = new ButtonGroup();
        radioGroup.add(alligatorRadioButton);
        radioGroup.add(sheepRadioButton);
    }

    private void setupLayout() {
        // Set up the main frame
        setTitle("Animal Counter");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10)); // Outer layout

        // North Panel for Welcome Message
        add(welcomeLabel, BorderLayout.NORTH);

        // Center Panel for Counts and Buttons
        JPanel centerPanel = new JPanel(new GridLayout(6, 1, 10, 10));

        // Row 1: Counts
        JPanel countPanel = new JPanel(new GridLayout(1, 2));
        countPanel.add(alligatorCountLabel);
        countPanel.add(sheepCountLabel);
        centerPanel.add(countPanel);

        // Row 2 & 3: Add Buttons
        centerPanel.add(addAlligatorButton);
        centerPanel.add(addSheepButton);

        // Row 4: Display Button
        centerPanel.add(displayCountsButton);

        // Row 5: Reset Options (Radio Buttons)
        JPanel radioPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 20, 0));
        radioPanel.add(new JLabel("Reset:"));
        radioPanel.add(alligatorRadioButton);
        radioPanel.add(sheepRadioButton);
        centerPanel.add(radioPanel);
        
        // Row 6: Reset Button
        centerPanel.add(resetCountsButton);

        add(centerPanel, BorderLayout.CENTER);

        // South Panel for Exit Button
        JPanel southPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        southPanel.add(exitButton);
        add(southPanel, BorderLayout.SOUTH);

        // Finalize frame
        pack();
        setLocationRelativeTo(null); // Center on screen
    }

    // Method to update count labels (called after every action that changes counts)
    private void updateCountLabels() {
        alligatorCountLabel.setText("Alligators: " + alligator.getCount());
        sheepCountLabel.setText("Sheep: " + sheep.getCount());
    }

    // e. Handle all button events via a single inner class event handler.
    private void registerListeners() {
        ButtonHandler handler = new ButtonHandler();
        
        addAlligatorButton.addActionListener(handler);
        addSheepButton.addActionListener(handler);
        displayCountsButton.addActionListener(handler);
        resetCountsButton.addActionListener(handler);
        exitButton.addActionListener(handler);
    }

    // The single inner class event handler
    private class ButtonHandler implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            if (e.getSource() == addAlligatorButton) {
                // Call the overridden method
                alligator.incrementCount(); 
            } 
            else if (e.getSource() == addSheepButton) {
                // Call the overridden method
                sheep.incrementCount(); 
            } 
            else if (e.getSource() == displayCountsButton) {
                // Just refreshes the display
            } 
            else if (e.getSource() == resetCountsButton) {
                // Check which radio button is selected
                if (alligatorRadioButton.isSelected()) {
                    alligator.resetCount();
                } else if (sheepRadioButton.isSelected()) {
                    sheep.resetCount();
                }
            } 
            else if (e.getSource() == exitButton) {
                System.exit(0);
            }

            // Update the display for all counting/reset actions
            updateCountLabels();
        }
    }
}


//AnimalCounterGUIAPP
import javax.swing.SwingUtilities;

public class AnimalCounterGUIApp {

    public static void main(String[] args) {
        // Ensures the GUI is created and updated on the Event Dispatch Thread (EDT)
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                // a. Make an instance of the AnimalCounterGUI class.
                AnimalCounterGUI gui = new AnimalCounterGUI();
                // b. Display the GUI
                gui.setVisible(true);
            }
        });
    }
}



