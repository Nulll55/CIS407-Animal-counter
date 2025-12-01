# CIS407-Animal-counter



-----Animal.java---
public abstract class Animal {

    protected int count;

    public Animal() {
        count = 0;
    }

    public void resetCount() {
        count = 0;
    }

    public int getCount() {
        return count;
    }
}
------countable.java-----
public interface Countable {
    void incrementCount();
}
----aligator.java---
import javax.swing.JOptionPane;

public class Alligator extends Animal implements Countable {

    private Sheep sheep; // reference needed to reduce sheep

    public Alligator(Sheep sheep) {
        super();
        this.sheep = sheep;
    }

    @Override
    public void incrementCount() {
        count += 1;    // add 1 alligator

        // If any sheep exist, remove ONE
        if (sheep.getCount() > 0) {
            sheep.count -= 1;
        }

        // Display message if alligators > sheep
        if (count > sheep.getCount()) {
            JOptionPane.showMessageDialog(null,
                    "Please add more sheep for the hungry alligators");
        }

        // If alligators drop to zero
        if (count == 0) {
            JOptionPane.showMessageDialog(null,
                    "No alligators now so the sheep are safe");
        }
    }
}
---Sheep.java(subclass)
import javax.swing.JOptionPane;

public class Sheep extends Animal implements Countable {

    public Sheep() {
        super();
    }

    @Override
    public void incrementCount() {
        count += 2;  // add 2 sheep

        // If no alligators exist, message
        // Note: GUI will check this, but including here covers all cases.
        // (Optional depending on your instructor)
    }
}
----animal count gui -------
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class AnimalCounterGUI extends JFrame {

    private Alligator alligator;
    private Sheep sheep;

    private JLabel lblAlligatorCount;
    private JLabel lblSheepCount;
    private JRadioButton rbAlligator;
    private JRadioButton rbSheep;

    public AnimalCounterGUI() {

        // Create animal objects
        sheep = new Sheep();
        alligator = new Alligator(sheep);

        setTitle("Animal Counter");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new GridLayout(6, 1));

        // Welcome Label
        JLabel lblWelcome = new JLabel("Welcome to the Animal Counter!", SwingConstants.CENTER);
        add(lblWelcome);

        // Count Labels
        lblAlligatorCount = new JLabel("Alligators: 0");
        lblSheepCount = new JLabel("Sheep: 0");

        JPanel panelCounts = new JPanel();
        panelCounts.add(lblAlligatorCount);
        panelCounts.add(lblSheepCount);
        add(panelCounts);

        // Buttons
        JButton btnAddAlligator = new JButton("Add Alligator");
        JButton btnAddSheep = new JButton("Add Sheep");

        JButton btnDisplay = new JButton("Display Counts");
        JButton btnReset = new JButton("Reset Counts");
        JButton btnExit = new JButton("Exit");

        JPanel panelButtons = new JPanel();
        panelButtons.add(btnAddAlligator);
        panelButtons.add(btnAddSheep);
        add(panelButtons);

        JPanel panelButtons2 = new JPanel();
        panelButtons2.add(btnDisplay);
        panelButtons2.add(btnReset);
        panelButtons2.add(btnExit);
        add(panelButtons2);

        // Radio Buttons
        rbAlligator = new JRadioButton("Alligator");
        rbSheep = new JRadioButton("Sheep");
        ButtonGroup bg = new ButtonGroup();
        bg.add(rbAlligator);
        bg.add(rbSheep);

        JPanel panelRadio = new JPanel();
        panelRadio.add(rbAlligator);
        panelRadio.add(rbSheep);
        add(panelRadio);

        // Shared event handler
        ButtonHandler handler = new ButtonHandler();

        btnAddAlligator.addActionListener(handler);
        btnAddSheep.addActionListener(handler);
        btnDisplay.addActionListener(handler);
        btnReset.addActionListener(handler);
        btnExit.addActionListener(handler);
    }

    private void updateLabels() {
        lblAlligatorCount.setText("Alligators: " + alligator.getCount());
        lblSheepCount.setText("Sheep: " + sheep.getCount());
    }

    private class ButtonHandler implements ActionListener {
        public void actionPerformed(ActionEvent e) {

            String cmd = e.getActionCommand();

            switch (cmd) {

                case "Add Alligator":
                    alligator.incrementCount();
                    updateLabels();
                    break;

                case "Add Sheep":
                    sheep.incrementCount();
                    updateLabels();
                    break;

                case "Display Counts":
                    JOptionPane.showMessageDialog(null,
                        "Alligators: " + alligator.getCount() +
                        "\nSheep: " + sheep.getCount());
                    break;

                case "Reset Counts":
                    if (rbAlligator.isSelected()) {
                        alligator.resetCount();
                    } else if (rbSheep.isSelected()) {
                        sheep.resetCount();
                    } else {
                        JOptionPane.showMessageDialog(null,
                                "Please select an animal to reset.");
                    }
                    updateLabels();
                    break;

                case "Exit":
                    System.exit(0);
                    break;
            }
        }
    }
}
-----animalCounterGUIApp.java---------
public class AnimalCounterGUIApp {

    public static void main(String[] args) {
        AnimalCounterGUI gui = new AnimalCounterGUI();
        gui.setVisible(true);
    }
}


