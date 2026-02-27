# Ex.No:5 Develop a simple calculator using android studio.

## AIM:

To develop a program to develop a simple calculator in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as calculator and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout using UI components in activity_main.xml.

Step 6: Display the calculator operation in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the text “calculator operation”.
Developed by: 212224243001
Registeration Number : ARAVINDAN SD
*/
```
Androidmanifest.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Mycalculator">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```
Activity_main.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#121212"
    android:padding="10dp">

    <TextView
        android:id="@+id/tvResult"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:text="0"
        android:textSize="50sp"
        android:textColor="#FFFFFF"
        android:gravity="bottom|end"
        android:padding="20dp"/>

    <GridLayout
        android:id="@+id/gridLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:columnCount="4">

        <Button android:text="AC" style="@style/btnClear"/>
        <Button android:text="C" style="@style/btnClear"/>

        <Button android:text="7" style="@style/btn"/>
        <Button android:text="8" style="@style/btn"/>
        <Button android:text="9" style="@style/btn"/>
        <Button android:text="/" style="@style/btnOp"/>

        <Button android:text="4" style="@style/btn"/>
        <Button android:text="5" style="@style/btn"/>
        <Button android:text="6" style="@style/btn"/>
        <Button android:text="*" style="@style/btnOp"/>

        <Button android:text="1" style="@style/btn"/>
        <Button android:text="2" style="@style/btn"/>
        <Button android:text="3" style="@style/btn"/>
        <Button android:text="-" style="@style/btnOp"/>

        <Button android:text="0" style="@style/btn" android:layout_columnSpan="2" android:layout_columnWeight="2" />
        <Button android:text="." style="@style/btn"/>
        <Button android:text="+" style="@style/btnOp"/>

        <Button android:text="=" style="@style/btnEqual"/>

    </GridLayout>

</LinearLayout>
```
MainActivity.java:
```
package com.example.mycalculator;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.GridLayout;
import android.widget.TextView;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {

    private TextView tvResult;
    private String expression = "";
    private boolean newCalculation = true;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        tvResult = findViewById(R.id.tvResult);
        GridLayout gridLayout = findViewById(R.id.gridLayout);

        for (int i = 0; i < gridLayout.getChildCount(); i++) {
            View child = gridLayout.getChildAt(i);
            if (child instanceof Button) {
                Button button = (Button) child;
                button.setOnClickListener(v -> onButtonClick(button));
            }
        }
    }

    private void onButtonClick(Button button) {
        String buttonText = button.getText().toString();

        switch (buttonText) {
            case "AC":
                expression = "";
                tvResult.setText("0");
                newCalculation = true;
                break;
            case "C":
                if (expression.length() > 0) {
                    expression = expression.substring(0, expression.length() - 1);
                    tvResult.setText(expression);
                }
                if (expression.length() == 0) {
                    tvResult.setText("0");
                }
                break;
            case "=":
                try {
                    double result = eval(expression);
                    tvResult.setText(String.valueOf(result));
                    expression = String.valueOf(result);
                    newCalculation = true;
                } catch (Exception e) {
                    tvResult.setText("Error");
                }
                break;
            default:
                if (newCalculation) {
                    expression = "";
                    newCalculation = false;
                }
                expression += buttonText;
                tvResult.setText(expression);
                break;
        }
    }

    public static double eval(final String str) {
        return new Object() {
            int pos = -1, ch;

            void nextChar() {
                ch = (++pos < str.length()) ? str.charAt(pos) : -1;
            }

            boolean eat(int charToEat) {
                while (ch == ' ')
                    nextChar();
                if (ch == charToEat) {
                    nextChar();
                    return true;
                }
                return false;
            }

            double parse() {
                nextChar();
                double x = parseExpression();
                if (pos < str.length())
                    throw new RuntimeException("Unexpected: " + (char) ch);
                return x;
            }

            // Grammar: 
            // expression = term | expression `+` term | expression `-` term
            // term = factor | term `*` factor | term `/` factor
            // factor = `+` factor | `-` factor | `(` expression `)`
            // | number | functionName factor | factor `^` factor

            double parseExpression() {
                double x = parseTerm();
                for (;;) {
                    if (eat('+'))
                        x += parseTerm(); // addition
                    else if (eat('-'))
                        x -= parseTerm(); // subtraction
                    else
                        return x;
                }
            }

            double parseTerm() {
                double x = parseFactor();
                for (;;) {
                    if (eat('*'))
                        x *= parseFactor(); // multiplication
                    else if (eat('/'))
                        x /= parseFactor(); // division
                    else
                        return x;
                }
            }

            double parseFactor() {
                if (eat('+'))
                    return parseFactor(); // unary plus
                if (eat('-'))
                    return -parseFactor(); // unary minus

                double x;
                int startPos = this.pos;
                if (eat('(')) { // parentheses
                    x = parseExpression();
                    eat(')');
                } else if ((ch >= '0' && ch <= '9') || ch == '.') { // numbers
                    while ((ch >= '0' && ch <= '9') || ch == '.')
                        nextChar();
                    x = Double.parseDouble(str.substring(startPos, this.pos));
                } else if (ch >= 'a' && ch <= 'z') { // functions
                    while (ch >= 'a' && ch <= 'z')
                        nextChar();
                    String func = str.substring(startPos, this.pos);
                    x = parseFactor();
                    if (func.equals("sqrt"))
                        x = Math.sqrt(x);
                    else if (func.equals("sin"))
                        x = Math.sin(Math.toRadians(x));
                    else if (func.equals("cos"))
                        x = Math.cos(Math.toRadians(x));
                    else if (func.equals("tan"))
                        x = Math.tan(Math.toRadians(x));
                    else
                        throw new RuntimeException("Unknown function: " + func);
                } else {
                    throw new RuntimeException("Unexpected: " + (char) ch);
                }

                if (eat('^'))
                    x = Math.pow(x, parseFactor()); // exponentiation

                return x;
            }
        }.parse();
    }
}
```

## OUTPUT

<img width="1919" height="1199" alt="image" src="https://github.com/user-attachments/assets/7cbfd4a0-611e-4e6e-8930-16a0cffc9853" />

<img width="1919" height="1199" alt="image" src="https://github.com/user-attachments/assets/059fbad4-3d1e-45ea-b3c7-e30516215c64" />



## RESULT
Thus a Simple Android Application develop a program to create simple calculator in Android Studio is developed and executed successfully.
