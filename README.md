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
    private Sheep sheep; 

    public Alligator(Sheep sheep) {
        this.count = 0;
        this.sheep = sheep;
    }

    @Override
    public void incrementCount() {
        this.count += 1; 

        if (sheep.getCount() > 0) {
            sheep.decrementCount(1); 
        }

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
    private Alligator alligator; 

    public Sheep(Alligator alligator) {
        this.count = 0;
        this.alligator = alligator;
    }

    public void decrementCount(int amount) {
        this.count = Math.max(0, this.count - amount); 
    }

    @Override
    public void incrementCount() {
        this.count += 2; 

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
    private Alligator alligator;
    private Sheep sheep;
    private JLabel welcomeLabel, alligatorCountLabel, sheepCountLabel;
    private JButton addAlligatorButton, addSheepButton, displayCountsButton, resetCountsButton, exitButton;
    private JRadioButton alligatorRadioButton, sheepRadioButton;
    private ButtonGroup radioGroup; 

    public AnimalCounterGUI() {
        // Initialize objects, handling the circular dependency
        alligator = new Alligator(null); 
        sheep = new Sheep(alligator);
        alligator = new Alligator(sheep); 

        initializeComponents();
        setupLayout();
        registerListeners();
        updateCountLabels();
    }

    private void initializeComponents() {
        welcomeLabel = new JLabel("Welcome to the Animal Counter", SwingConstants.CENTER);
        welcomeLabel.setFont(new Font("SansSerif", Font.BOLD, 16));
        alligatorCountLabel = new JLabel("Alligators: 0", SwingConstants.LEFT);
        sheepCountLabel = new JLabel("Sheep: 0", SwingConstants.LEFT);
        
        addAlligatorButton = new JButton("Add Alligator (+1)");
        addSheepButton = new JButton("Add Sheep (+2)");
        displayCountsButton = new JButton("Display Counts");
        resetCountsButton = new JButton("Reset Counts");
        exitButton = new JButton("Exit");

        alligatorRadioButton = new JRadioButton("Alligator", true); // Default selected
        sheepRadioButton = new JRadioButton("Sheep");
        
        radioGroup = new ButtonGroup();
        radioGroup.add(alligatorRadioButton);
        radioGroup.add(sheepRadioButton);
    }

    private void setupLayout() {
        setTitle("Animal Counter");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10)); 

        add(welcomeLabel, BorderLayout.NORTH);

        JPanel centerPanel = new JPanel(new GridLayout(6, 1, 10, 10));
        JPanel countPanel = new JPanel(new GridLayout(1, 2));
        countPanel.add(alligatorCountLabel);
        countPanel.add(sheepCountLabel);
        centerPanel.add(countPanel);
        centerPanel.add(addAlligatorButton);
        centerPanel.add(addSheepButton);
        centerPanel.add(displayCountsButton);

        JPanel radioPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 20, 0));
        radioPanel.add(new JLabel("Reset:"));
        radioPanel.add(alligatorRadioButton);
        radioPanel.add(sheepRadioButton);
        centerPanel.add(radioPanel);
        centerPanel.add(resetCountsButton);

        add(centerPanel, BorderLayout.CENTER);

        JPanel southPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        southPanel.add(exitButton);
        add(southPanel, BorderLayout.SOUTH);

        pack();
        setLocationRelativeTo(null); 
    }

    private void updateCountLabels() {
        alligatorCountLabel.setText("Alligators: " + alligator.getCount());
        sheepCountLabel.setText("Sheep: " + sheep.getCount());
    }

    private void registerListeners() {
        ButtonHandler handler = new ButtonHandler();
        addAlligatorButton.addActionListener(handler);
        addSheepButton.addActionListener(handler);
        displayCountsButton.addActionListener(handler);
        resetCountsButton.addActionListener(handler);
        exitButton.addActionListener(handler);
    }

    private class ButtonHandler implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            if (e.getSource() == addAlligatorButton) {
                alligator.incrementCount(); 
            } else if (e.getSource() == addSheepButton) {
                sheep.incrementCount(); 
            } else if (e.getSource() == resetCountsButton) {
                if (alligatorRadioButton.isSelected()) {
                    alligator.resetCount();
                } else if (sheepRadioButton.isSelected()) {
                    sheep.resetCount();
                }
            } else if (e.getSource() == exitButton) {
                System.exit(0);
            }
            updateCountLabels();
        }
    }
}

//AnimalCounterGUIAPP
import javax.swing.SwingUtilities;

public class AnimalCounterGUIApp {

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                AnimalCounterGUI gui = new AnimalCounterGUI();
                gui.setVisible(true);
            }
        });
    }
}



