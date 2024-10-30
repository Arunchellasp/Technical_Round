# Technical_Round
Demo video link: https://drive.google.com/file/d/11S3YXM3jVS_6D73_30epYzzbB6Rcgk3G/view?usp=sharing


Mathematical Expression for voltage conversion:

Millivoltage = (ADC value from controller × maximum voltage) / maximum bit value of the controller

Voltage = millivoltage/1000
​
 
Connection:



<img width="572" alt="image" src="https://github.com/user-attachments/assets/db7c60f3-61d6-4a24-a7f8-2dbdca3d5c21">



OUTPUT Live Expression:


<img width="370" alt="image" src="https://github.com/user-attachments/assets/f982d0cf-d4ed-402b-b109-421e2b0c847d">


Pinout & configration:

<img width="498" alt="image" src="https://github.com/user-attachments/assets/0dce5497-dcd4-4f2f-a9be-259cca8c139d">



System view:


<img width="611" alt="image" src="https://github.com/user-attachments/assets/213a91a2-f03f-4073-bbae-71e70405e931">



summery:

This code continuously reads analog values from two ADC channels (7 and 8), converts the raw ADC readings to voltages, and maps these to PWM duty cycles for two separate PWM channels. Each loop iteration:

1.Reads ADC values from channels 7 and 8.


2.Converts ADC values to millivolts and volts.


3.Maps ADC values to PWM duty cycles with a scaling factor.


4.Updates PWM outputs on TIM2 channels 2 and 3 based on the ADC readings.





Line-by-line explanation with comments for each key section:

// Initialize counter for tracking loop iterations
int count = 0;

// Variables to store raw ADC values
uint16_t readValue1;
uint16_t readValue2;

// Variables for PWM output values based on ADC readings
uint16_t PWM1;
uint16_t PWM2;

// Variables to store voltage in millivolts and volts
uint16_t voltage1_mV;
uint16_t voltage2_mV;
float voltage1;
float voltage2;

// Infinite loop to continuously read ADC values and update PWM
while (1)
{
    count++;                      // Increment loop counter for tracking iterations
    HAL_Delay(500);               // Delay of 500 milliseconds

    // Configure ADC settings for first reading
    sConfigPrivate.Rank = 1;               // Set ADC rank
    sConfigPrivate.SamplingTime = ADC_SAMPLETIME_3CYCLES; // Set ADC sampling time

    // Configure ADC to read from Channel 7
    sConfigPrivate.Channel = ADC_CHANNEL_7;
    HAL_ADC_ConfigChannel(&hadc1, &sConfigPrivate);     // Apply ADC channel configuration
    HAL_ADC_Start(&hadc1);                             // Start ADC conversion
    HAL_ADC_PollForConversion(&hadc1, 1000);           // Wait for ADC conversion to complete (1 second timeout)
    readValue1 = HAL_ADC_GetValue(&hadc1);             // Store raw ADC value from Channel 7
    HAL_ADC_Stop(&hadc1);                              // Stop ADC conversion

    // Configure ADC to read from Channel 8
    sConfigPrivate.Channel = ADC_CHANNEL_8;
    HAL_ADC_ConfigChannel(&hadc1, &sConfigPrivate);     // Apply ADC channel configuration
    HAL_ADC_Start(&hadc1);                             // Start ADC conversion
    HAL_ADC_PollForConversion(&hadc1, 1000);           // Wait for ADC conversion to complete (1 second timeout)
    readValue2 = HAL_ADC_GetValue(&hadc1);             // Store raw ADC value from Channel 8
    HAL_ADC_Stop(&hadc1);                              // Stop ADC conversion

    // Convert ADC values to millivolts
    voltage1_mV = (readValue1 * 3300) / 4095;          // Scale ADC value (0-4095) to millivolts
    voltage2_mV = (readValue2 * 3300) / 4095;

    // Convert millivolts to volts for further calculations
    voltage1 = voltage1_mV / 1000.0;                   // Convert millivolts to volts (float division)
    voltage2 = voltage2_mV / 1000.0;

    // Scale raw ADC values to set PWM duty cycle values (arbitrary divisor of 7 here)
    PWM1 = readValue1 / 7;
    PWM2 = readValue2 / 7;

    // Set PWM duty cycle on specific timer channels based on ADC readings
    __HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_2, PWM1); // Update PWM on TIM2 Channel 2
    __HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_3, PWM2); // Update PWM on TIM2 Channel 3

    HAL_Delay(100);                                    // Short delay before next loop iteration
}

