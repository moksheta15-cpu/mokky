import com.google.zxing.BarcodeFormat;
import com.google.zxing.WriterException;
import com.google.zxing.qrcode.QRCodeWriter;
import com.google.zxing.common.BitMatrix;

import javax.swing.*;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import javax.imageio.ImageIO;

public class TapbackApp {

    private JTextField nameField;
    private JTextField phoneField;
    private JLabel qrLabel;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(TapbackApp::new);
    }

    public TapbackApp() {
        JFrame frame = new JFrame("Tapback – Java Test");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(500, 400);

        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(5, 1, 10, 10));

        nameField = new JTextField();
        phoneField = new JTextField();

        JButton generateButton = new JButton("Generate QR");
        qrLabel = new JLabel("", SwingConstants.CENTER);

        generateButton.addActionListener(e -> generateQR());

        panel.add(new JLabel("Full Name:"));
        panel.add(nameField);
        panel.add(new JLabel("Phone:"));
        panel.add(phoneField);
        panel.add(generateButton);

        frame.add(panel, BorderLayout.NORTH);
        frame.add(qrLabel, BorderLayout.CENTER);

        frame.setVisible(true);
    }

    private void generateQR() {
        String name = nameField.getText().trim();
        String phone = phoneField.getText().trim();

        if (name.isEmpty() || phone.isEmpty()) {
            JOptionPane.showMessageDialog(null, "Please enter both name and phone.");
            return;
        }

        String payload = "{ \"id\":\"" + System.currentTimeMillis() +
                "\", \"name\":\"" + name +
                "\", \"phone\":\"" + phone + "\" }";

        try {
            QRCodeWriter qrCodeWriter = new QRCodeWriter();
            BitMatrix bitMatrix = qrCodeWriter.encode(payload, BarcodeFormat.QR_CODE, 250, 250);

            BufferedImage qrImage = new BufferedImage(250, 250, BufferedImage.TYPE_INT_RGB);
            for (int x = 0; x < 250; x++) {
                for (int y = 0; y < 250; y++) {
                    qrImage.setRGB(x, y, bitMatrix.get(x, y) ? Color.BLACK.getRGB() : Color.WHITE.getRGB());
                }
            }

            qrLabel.setIcon(new ImageIcon(qrImage));

            // Optional: save PNG locally
            ImageIO.write(qrImage, "png", new File("tapback-qr.png"));
            System.out.println("QR saved as tapback-qr.png");

        } catch (WriterException | java.io.IOException ex) {
            ex.printStackTrace();
        }
    }
}
