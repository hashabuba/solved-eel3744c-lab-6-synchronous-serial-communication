Download Link: https://assignmentchef.com/product/solved-eel3744c-lab-6-synchronous-serial-communication
<br>
<h1>OBJECTIVES</h1>

<ul>

 <li>Understand general concepts regarding synchronous serial communication.</li>

 <li>Become familiar with the <em>Serial Peripheral Interface</em> (<em>SPI</em>) communication protocol and how to apply it with the <em>ATxmega128A1U</em>.</li>

 <li>Interface with an external <em>inertial measurement unit</em> (<em>IMU</em>) sensor package, by way of the SPI communication protocol.</li>

 <li>Stream and plot real-time 3D (XYZ) acceleration data using the <em>SPI</em> and <em>USART</em> systems of the <em>ATxmega128A1U</em>.</li>

</ul>

<h1>INTRODUCTION</h1>

In general, systems that use <em>synchronous serial communication</em> utilize a common clock signal to determine when to send, receive, or sample data. In fact, all synchronous communication is dependent on such a signal. In contrast, for devices communicating with an asynchronous serial communication protocol, there exists no synchronization signal. Instead, a common transfer rate must be upheld by the systems, or else, data received could be interpreted incorrectly, or even entirely missed.

Synchronous serial communication is normally preferred over asynchronous serial communication because higher data transfer rates can generally be achieved. The main reason for this is that asynchronous serial systems must normally include within all data transmissions some bits for synchronization and “error-checking”, which effectively reduces any possible rate of <em>data</em> <em>transfer</em>.

One popular synchronous serial communication protocol is <em>Serial Peripheral Interface </em>(<em>SPI</em>). With SPI, full-duplex communication is possible between two or more devices, although only one device may act as a controlling <em>master</em> – every other connected device must act as a responding <em>slave</em>. Although there may be only one master, it is possible that the <em>role of master </em>be assigned dynamically. However, for this to be done properly, some form of “handshaking” is required.

When communication is to occur between some master and slave(s), <em>the master device is always responsible for initiating the communication</em>. Normally, before some communication may occur, a slave must be enabled by way of a <em>chip select</em> (<em>CS</em>), or s<em>lave select</em> (<em>SS</em>), signal. In general, such a signal is either controlled by the master or is always driven <em>true</em>. To start communication once some set of slaves is enabled, the master generates a clock signal, often referred to as the <em>serial clock</em> (<em>SCK, SPC</em>). Upon some edge of each generated clock pulse, either the rising edge or falling edge, each of the master and slave(s) will shift out a single bit of data from an internal shift register, where each has their own. Furthermore, each device is to “sample” some bit of data on the opposite edge of the same clock pulse. Any data shifted into a slave from some master is transmitted via a signal most commonly referred to as <em>Master-Output-Slave-Input</em>

(<em>MOSI</em>), and any data shifted into a master from some slave is transmitted via a signal most commonly referred to as  <em>Master-Input-Slave-Output</em> (<em>MISO</em>). In general, either or both of some MOSI/MISO signals can be used; in other words, it is possible for

[1] a master to transmit to, but not receive from, some slave(s), [2] a master to receive from, but not transmit to, some slave(s), and  [3] a master to both transmit to and receive from some slave(s). For both [2] and [3], it must be ensured that no two slave devices share a chip select signal, for else there would be bus contention.

Although SPI is a very common synchronous serial communication protocol, there exist many others; some others include <em>InterIntegrated Circuit </em>(<em>IIC</em>, or more commonly, <em>I<sup>2</sup>C</em>), <em>Universal Serial Bus</em> (<em>USB</em>), and <em>Controller Area Network</em> (<em>CAN</em>). Moreover, the synchronous mode within the USART system of the <em>ATxmega128A1U</em>, the ‘S’ of “USART”, provides access to another synchronous serial communication protocol, which is very similar to SPI.

<h1>LAB STRUCTURE</h1>

In this lab, you will utilize synchronous serial communication by way of the SPI protocol. More specifically, you will learn how to configure and use the <em>SPI</em> system of the <em>ATxmega128A1U</em>, for the purpose of communicating with an <em>LSM6DS3 inertial measurement unit </em>(<em>IMU</em>) chip, located on the <em>OOTB Robotics Backpack</em>. The <em>LSM6DS3 </em>contains a Micro-Electro-Mechanical System (MEMS)  3D digital accelerometer and a MEMS 3D digital gyroscope<a href="#_ftn1" name="_ftnref1"><strong><sup>[1]</sup></strong></a>. Once SPI communication with the IMU is properly established, you will generate real-time plots of linear acceleration on your computer, by streaming IMU sensor data to a data visualization program, <em>SerialPlot</em>, via the relevant USART module.




<h1>REQUIRED MATERIALS                                       SUPPLEMENTAL MATERIALS</h1>

<ul>

 <li><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8331_%20XMEGA_AU_Manual.pdf">Atmel </a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8331_%20XMEGA_AU_Manual.pdf"><em>ATxmega128A1U</em></a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8331_%20XMEGA_AU_Manual.pdf"> AU Manual (doc8331)</a> <a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8385_ATxmega128A1U_Manual.pdf">Atmel </a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8385_ATxmega128A1U_Manual.pdf"><em>ATxmega128A1U</em></a><a href="https://mil.ufl.edu/3744/docs/XMEGA/doc8385_ATxmega128A1U_Manual.pdf"> Manual (doc8385)</a></li>

 <li><a href="https://mil.ufl.edu/3744/docs/uPAD2p0/LSM6DS3TR%20(Accel,%20Gyro).pdf"><em>LSM6DS3 Datasheet</em></a> <a href="https://mil.ufl.edu/3744/docs/XMEGA/Atmel-2585-Setup-and-Use-of-the-SPI_ApplicationNote_AVR151.pdf"><em>Setup and Use of the SPI</em></a><a href="https://mil.ufl.edu/3744/docs/XMEGA/Atmel-2585-Setup-and-Use-of-the-SPI_ApplicationNote_AVR151.pdf"> (doc2585)</a></li>

 <li>Relevant skeleton code files (.c and .h): <a href="https://mil.ufl.edu/3744/labs/lab6_lsm6ds3_u20/lsm6ds3_files.zip">zip</a></li>

 <li><em>OOTB µPAD</em>, with USB A/B cable</li>

 <li><em>OOTB Robotics Backpack</em>,with accompanying schematic</li>

 <li><strong>D</strong>igital <strong>A</strong>nalog <strong>D</strong>iscovery (<strong>DAD</strong>) kit, with <em>WaveForms</em></li>

</ul>

<h1>PRE-LAB PROCEDURE</h1>

<strong><u>REMINDER OF LAB POLICY</u></strong>

You must re-read the <a href="https://mil.ufl.edu/3744/admin/Lab%20Rules%20%26%20Policies.pdf"><em>Lab Rules &amp; Policies</em></a> before submitting any pre-lab assignment and before attending any lab.

<h2>1. INTRODUCTION TO SPI AND THE <em>LSM6DS3</em></h2>




In this section, you will begin to become familiar with the SPI protocol, the SPI system available within the <em>ATxmega128A1U</em>, and the <em>LSM6DS3 </em>inertial measurement unit (IMU). When proceeding, keep the following in mind:

<ul>

 <li>The SPI serial communication protocol, like any other digital communication protocol, is just a predefined system of rules for performing data transfer between multiple components via a set of digital signals.</li>

 <li>The <em>SPI</em> system within the <em>ATxmega128A1U</em> was designed to simplify the procedure of emulating the SPI protocol.</li>

 <li>From the perspective of the microcontroller, the <em>LSM6DS3</em> IMU is an external peripheral.</li>

</ul>

1.1. Carefully read § 22 (<em>SPI – Serial Peripheral Interface</em>) of the 8331 manual.

Generally, for design simplicity, many devices (especially peripherals) are built to only support a subset of possible SPI configurations. Fortunately, the SPI system within the <em>ATxmega128A1U</em> was built to support most of the possible SPI configurations, and thus, is able to communicate with many types of components. However, because of this flexibility, there are several considerations to make when utilizing this SPI system (and often when using the SPI protocol in general):

<ul>

 <li>Which device(s) should be given the role of master and which device(s) should be given the role of slave?</li>

 <li>How will the slave device(s) be enabled? If a slave select is utilized, rather than just have the device(s) be permanently enabled, which pin(s) will be used?</li>

 <li>What is the order of data transmission? Is the MSb or LSb transmitted first?</li>

 <li>In regard to the relevant clock signal, should data be latched on a rising edge or on a falling edge?</li>

 <li>What is the maximum serial clock frequency that can be utilized by the relevant devices?</li>

</ul>

Throughout this lab, you will utilize the SPI protocol to communicate with an <em>LSM6DS3 </em>IMU peripheral chip located on the <em>OOTB Robotics Backpack</em>, for the purposes of interfacing with a MEMS 3D digital accelerometer and, optionally, a MEMS 3D digital gyroscope.

Like with the <em>ATxmega128A1U</em>, the <em>LSM6DS3 </em>utilizes memory-mapped registers to store data relevant to its internal components. However, instead of utilizing a parallel address bus and parallel data bus to access these memory-mapped registers, like the <em>ATxmega128A1U</em>, the <em>LSM6DS3 </em>utilizes the SPI (or I<sup>2</sup>C) protocol.

It is important to recognize that the <em>LSM6DS3 </em>component located on the <em>OOTB Robotics Backpack </em>was designed to utilize specific I/O pins on the <em>ATxmega128A1U</em>. Additionally, to allow the potential for both SPI and I<sup>2</sup>C communication, certain signals were designed to be multiplexed with a digital switch (more specifically, an analog, bidirectional, 2-input multiplexer) on the <em>OOTB Robotics Backpack</em>. Thus, there are additional signals within the <em>OOTB Robotics Backpack </em>that must be directly controlled by the microcontroller (with I/O port assignments, just like in previous labs), before attempting to configure the <em>LSM6DS3</em>.

Below, you will begin to become familiar with the <em>LSM6DS3</em> component and understand how it was designed to connect to your microcontroller.

1.2. Read through th<a href="https://mil.ufl.edu/3744/docs/uPAD2p0/LSM6DS3TR%20(Accel,%20Gyro).pdf">e </a><a href="https://mil.ufl.edu/3744/docs/uPAD2p0/LSM6DS3TR%20(Accel,%20Gyro).pdf"><em>LSM6DS3</em></a><a href="https://mil.ufl.edu/3744/docs/uPAD2p0/LSM6DS3TR%20(Accel,%20Gyro).pdf"> datasheet</a><a href="https://mil.ufl.edu/3744/docs/uPAD2p0/LSM6DS3TR%20(Accel,%20Gyro).pdf">.</a> Pay extra attention to §§ 2, 4, 6.2, 7, and 8, as well as to Table 2 (focus on the SPI function of each pin), Table 6, and Table 9.

1.3. Determine which signals from the <em>ATxmega128A1U </em>will be utilized to communicate with the <em>LSM6DS3 </em>chip on the <em>OOTB Robotics Backpack</em>. Refer to the appropriate schematic(s) and manual(s).

<h3>PRE-LAB EXERCISES</h3>

<table width="735">

 <tbody>

  <tr>

   <td width="254"><strong>2.</strong> <strong>COMMUNICATING WITH SPI </strong></td>

  </tr>

 </tbody>

</table>

<ol>

 <li>In regard to SPI communication that is to exist between the relevant <em>ATxmega128A1U </em>and <em>LSM6DS3 </em>chips, answer each of the questions within the previously given bulleted list.</li>

</ol>




Throughout the next few sections, you will incrementally create several routines with the “C” programming language, for the purpose of communicating with the <em>LSM6DS3 </em>device. Additionally, you will perform experiments when appropriate, to verify your work.

First and foremost, you will design “C” functions to utilize the SPI system within the <em>ATxmega128A1U.</em> The functions that you will create should allow for a somewhat generic use of the SPI system and should follow the provided <strong><em>spi.h </em></strong>and <strong><em>spi.c</em></strong> files.

2.1. Within the provided <strong><em>spi.c </em></strong>file, complete the “C” function, <strong><em>void spi_init(void)</em></strong>, to initialize the appropriate SPI module within the <em>ATxmega128A1U</em> as well as the appropriate control signals on the <em>µPAD</em>, for the purpose of communicating with the relevant <em>LSM6DS3</em>.

2.1.1. Make sure that you select the bit transmission order (MSb or LSb) expected by the <em>LSM6DS3</em>, and that you do not choose a SPI clock frequency that is too fast for the <em>LSM6DS3</em>. For more details, refer to § 22.3 (<em>Master Mode</em>) of the 8331 manual, § 33.2 (<em>Alternate </em>

<em>Pin Functions</em>) of the 8385 manual, and any relevant schematics.

2.2. Write a second “C” function, <strong><em>void</em></strong><em> <strong>spi_write(uint8_t data)</strong></em>, to transmit a single byte of data from the master device (the <em>ATxmega128A1U</em>), and then wait for the SPI transmission to be complete.

2.2.1. To wait for the transmission to be complete, you should poll a specific flag in the relevant SPI status register. Interrupts should not be used for this purpose, as it inhibits portable code. (Note that more advanced programming techniques, which are outside the scope of this course, could be used to circumvent such a portability issue.) After the SPI transmission is complete, your function should terminate. Do <em>not</em> enable or disable any slave devices within this function, as multiple sequential calls to the function are possible, rendering the use of multiple enables/disables inefficient. Such enabling and disabling should be handled within a separate routine, which is discussed later on.

2.3. Write a third “C” function, <strong><em>uint8_t spi_read(void)</em></strong>, to read a single byte of data from a connected slave device.

2.3.1. This function should write some arbitrary byte of data to the SPI data register, to trigger an exchange of data between your microcontroller and the <em>LSM6DS3</em>, and then after the transmission is complete, have the function return the contents of the data register. Remember that, with SPI communication, data is shifted in from a slave device at the <em>same time</em> that data is shifted out from the master device. To be able to read in a byte of data, you must send out a byte of data. The byte of data that you choose to transmit in order to accomplish this task is arbitrary, e.g., it could be 0xFF, 0x37, etc. (The slave device will know to not save any data being received from the master during this time.) Just as with the <strong><em>spi_write </em></strong>routine above, do <em>not</em> enable or disable any slave devices within this function.

Now, you will create a “C” program and utilize your DAD to verify <em>transmit</em> functionality for the relevant SPI module.

<strong>NOTE: </strong>The following method could also be used to verify <em>receive</em> functionality, although a different, more intuitive technique will be used to test such functionality, in the following section of this lab.

2.4. Write a simple “C” program, <strong>lab6_2.c</strong>, to initialize the relevant SPI module and to continually transmit <strong>0x53 </strong>from this same module. Utilize the relevant “C” functions previously written by yourself. Also, to simulate the <em>LSM6DS3</em> slave device being enabled/disabled, assert an available I/O port pin <em>low</em> before each transmission (i.e., simulate the process of enabling the device), and de-assert the same pin after each transmission (i.e., set the relevant pin <em>high</em>, or simulate the process of disabling the device).

<strong> </strong>

To verify that the SPI module is correctly transmitting <strong>0x53</strong>, you will view all appropriate SPI signals with the <em>SPI</em> digital bus analyzer function of the <em>Logic </em>(LSA) feature within <em>Waveforms</em>. See the left image of Figure 1 to determine where this feature may be accessed from within the <em>Logic </em>feature.

<strong>Figure 1.</strong> (left) Accessing the SPI function within the <em>Logic</em> feature; (right) the <em>Add SPI</em> menu.

As shown in the right image of Figure 1, the SPI function can be used to view the three relevant SPI signals: <strong><em>Select</em></strong> (the signal used to enable a slave device, usually denoted as <em>chip select</em> [<em>CS</em>] or <em>slave select</em> [<em>SS</em>]), <strong><em>Clock</em></strong> (the clock signal used to synchronize any SPI connected devices, usually denoted as <em>serial clock</em> [<em>SCK</em>], or something similar, such as <em>serial port clock</em> [<em>SPC</em><strong>]</strong> in the <em>LSM6DS3</em> datasheet), and <strong><em>Data</em> </strong>(the signal used to transmit data to or receive data from a slave device, which can represented by several common names, e.g., <em>Master-Out-SlaveIn</em> [<em>MOSI</em>], <em>Master-In-Slave-Out</em> [<em>MISO</em>], <em>Slave Data In</em> [<em>SDI</em>], and <em>Slave Data Out</em> [<em>SDO</em>]).

2.5. Within the <em>Logic</em> feature of <em>Waveforms</em>, select <em>SPI</em> from the “<em>Click to Add channel</em>” dropdown menu, depicted in the left image of Figure 1. You will be prompted with the <em>Add SPI</em> menu, an example of which is depicted in the right image of Figure 1. Configure the relevant settings. Refer to the relevant schematics, if necessary.

2.6. Use the program created above, <strong>lab6_2.c</strong>, along with the SPI digital bus analyzer function of your DAD, to verify that your microcontroller is correctly transmitting <strong>0x53</strong>. To be able to probe the relevant pins, you may either [1] remove any backpack connected to the <em>µPAD</em> or [2] utilize the <em>J2 </em>header of the <em>OOTB Robotics Backpack</em>. The chosen DIO pin on the DAD for SPI data should connect to the appropriate MOSI signal. You may wish to use the slave select signal as a falling-edge trigger source within the <em>Logic </em>feature. You can also use a “protocol trigger” to trigger the LSA when an SPI transmission starts.

2.7. Take a screenshot of the LSA measuring a single, full byte of data being transmitted. Include all relevant signals. Make sure that you choose an appropriate time base such that a single transmission is clearly visible.

<table width="735">

 <tbody>

  <tr>

   <td width="336"><strong>3.</strong> <strong>COMMUNICATING WITH THE <em>LSM6DS3</em> </strong></td>

  </tr>

 </tbody>

</table>

In this section, you will verify <em>receive</em> functionality by interfacing with the external <em>LSM6DS3 </em>IMU. To accomplish this, you will create a new “C” program, <strong>lab6_3.c</strong>, to read from an accelerometer register within the <em>LSM6DS3</em>. However, before writing this program, you will first design two “C” functions to allow you to read from or write to <em>any</em> register within the <em>LSM6DS3</em>.

Overall, the <em>LSM6DS3</em> has a plethora of configuration registers for its internal accelerometer (and gyroscope), just like our microcontroller does for its internal peripherals. However, unlike when accessing a register within the <em>ATxmega128A1U</em>, where parallel busses are utilized to communicate address and data values, SPI (or I<sup>2</sup>C) must be used when reading from or writing to some register within the <em>LSM6DS3</em>.

3.1. Re-read and understand the sections of the <em>LSM6DS3</em> datasheet that concern configuration registers and how they are to be accessed.

<strong>NOTE:</strong> Any <em>LSM6DS3</em> register that has a name suffixed with “_XL” is associated with the built-in accelerometer. Any register that has a name suffixed with “_G” is associated with the builtin gyroscope.

As stated above, you will design two “C” functions to allow you to read from or write to <em>any</em> register within the <em>LSM6DS3</em>. These functions are described below.

<strong><em>void lsm6ds3_write(uint8_t reg_addr, uint8_t data)</em></strong> – Writes a single byte of data, `<em>data`</em>, to the address <em>`reg_addr`</em>, which is meant to be associated with an <em>LSM6DS3</em> register.

<strong><em>uint8_t lsm6ds3_read(uint8_t reg_addr)</em></strong> – Returns a single byte of data from an <em>LSM6DS3</em> register associated with the address  <em>`reg_addr`</em>.

When writing code to configure or utilize the <em>LSM6DS3</em>, it is highly recommended that you download and utilize the relevant skeleton files provided for this lab. The given `<em>lsm6ds3.h` </em>header file is primarily meant to provide <em>declarations</em> for the above two functions (as well as another that we will be written in the following section of this lab), whereas the given `<em>lsm6ds3.c` </em>file is primarily meant to provide <em>definitions</em> for these two functions (and the third that has yet to be discussed). Separately, the <em>`lsm6ds3_registers.h`</em> header file is primarily meant to provide some useful symbols for address values associated with memory-mapped registers located within the <em>LSM6DS3</em>, somewhat similar to the AVR include file

<em>`ATxmega128A1Udef.inc`</em>. See Figure 2 for a few examples of the symbols provided within this file. Recall that you can define your own set of constants, or condense certain aspects of “C” code, with macros. (In “C”, a macro is defined with the `<em>#define`</em> preprocessor directive.)

Following this, to create the necessary “C” functions to write to or read from <em>any</em> register within the <em>LSM6DS3</em>, here are the order of events that should occur:

<ul>

 <li>Enable the <em>LSM6DS3 </em>via the relevant chip select signal. (Refer to how the <em>OOTB Robotics Backpack</em> is designed.)</li>

 <li>Send the appropriate amount of data to the <em>LSM6DS3</em>, based on the timing diagram given in the <em>LSM6DS3</em></li>

 <li>Disable the <em>LSM6DS3 </em>via the relevant chip select signal.</li>

</ul>

<strong>Figure 2. </strong>Some code segment taken from <em>`lsm6ds3_registers.h` </em>

<ul>

 <li>Create a “C” function to be able to write to any of the registers within the <em>LSM6DS3</em>, <strong><em>void lsm6ds3_write(uint8_t reg_addr, uint8_t data)</em></strong>, as well as another “C” function to be able to read from any of the available registers, <strong><em>uint8_t lsm6ds3_read(uint8_t reg_addr)</em></strong>. Whenever appropriate, utilize other “C” functions previously written by yourself.</li>

</ul>

Now, we will verify <em>receive</em> functionality of the relevant SPI module. Within the <em>LSM6DS3</em>, there exists a predefined register to identify the device ID. This register, denoted by WHO_AM_I (refer to § 9.11 in the <em>LSM6DS3 </em>datasheet), returns the default value of <strong>0x69</strong>. If you are able to create a program that successfully reads from the WHO_AM_I register, you should be able to reason that your SPI interface is appropriately designed.

<ul>

 <li>Create a “C” program, <strong>c</strong>, to read from the WHO_AM_I register within the <em>LSM6DS3 </em>and store the read value into some temporary variable, so that the read value may be verified through the relevant debug window(s) of <em>Atmel Studio</em>.</li>

</ul>

<table width="735">

 <tbody>

  <tr>

   <td width="572"><strong>4.</strong> <strong>CONFIGURING THE <em>LSM6DS3</em> ACCELEROMETER </strong>

    <table width="733">

     <tbody>

      <tr>

       <td width="389">Now that you have a set of functions that allow you to configure accelerometer registers within the <em>LSM6DS3</em>, you need to actually configure the accelerometer! However, before attempting to do so, read through the following comments,</td>

       <td width="24">Ø</td>

       <td width="320">Before the accelerometer is configured or utilized at all, it is recommended that you first perform a software reset of the entire <em>LSM6DS3 </em>device. Refer to information regarding the <em>CTRL3_C </em> register.</td>

      </tr>

     </tbody>

    </table>questions, and requirements:</td>

  </tr>

 </tbody>

</table>

<ul>

 <li>Connect the <em>OOTB Robotics Backpack </em>to the <em>µPAD</em>, if necessary. Test the <em>receive </em>functionality of your system by debugging your program within <em>Atmel Studio</em>. Determine whether or not the expected value was read by your SPI system by any appropriate means, e.g., by way of a <em>Watch</em> window within <em>Atmel Studio</em>, etc. If you do not receive the expected data, use the LSA on your DAD board to debug the system. Probe the relevant pins via the <em>J2</em> header of the <em>OOTB Robotics </em>Backpack, and compare measurements received from the LSA to the relevant timing diagrams provided within the <em>LSM6DS3</em></li>

</ul>

<ul>

 <li>For this lab, you must have the accelerometer acquire acceleration forces from each of the X, Y, and Z coordinate directions simultaneously. How do you specify that the accelerometer should do so? Refer to information regarding the <em>CTRL9_XL</em></li>

 <li>What should the<em> full-scale selection </em>be for the accelerometer? Overall, this criterion dictates the limits of acceleration force that the accelerometer can measure, and, in turn, dictates how precise an accelerometer measurement can be. As an example, of those full-scale settings provided by the <em>LSM6DS3</em>, “±2g” would provide the lowest limits, but would, in turn, provide the highest precision. A setting of “±2g” would likely not work well for fighter jets, but it likely would for the contexts of our course. Refer to information regarding the <em>CTRL1_XL</em></li>

 <li>What should the <em>output data rate</em> be for the accelerometer? Overall, this criterion dictates how fast the accelerometer will output all of the relevant data. When deciding this rate, the relevant serial communication rate(s) and microcontroller system clock frequency should be taken into account. For our purposes, 208 Hz would likely be good, but some other rate, either faster or slower, might be more appropriate. Refer to information regarding the <em>CTRL1_XL</em></li>

 <li>The <em>LSM6DS3</em> is designed to be able to generate an interrupt signal upon the accelerometer completing an acceleration measurement, which can be used to signal that data is ready</li>

</ul>

to be read from the accelerometer. <em>In this lab, such an interrupt signal must be used to trigger an I/O interrupt on your microcontroller, instead of this signal being manually polled.</em> Refer to information regarding the <em>INT1_CTRL </em>register, as well as the relevant schematics. Which <em>input sense configuration </em>should be utilized by the relevant I/O pin of your microcontroller?

Overall, for this lab, you must do the following when initializing the <em>LSM6DS3</em> accelerometer:

<ol>

 <li>Perform a software reset of the <em>LSM6DS3</em>, via the <em>CTRL3_C </em></li>

 <li>Configure the <em>CTRL1_XL</em>, <em>CTRL9_XL</em>, and <em>INT1_CTRL</em> registers, in some appropriate manner.</li>

</ol>

<ul>

 <li>Configure an I/O port interrupt to trigger upon the accelerometer completing a measurement.

  <ul>

   <li>Create a “C” function, <strong><em>void lsm6ds3_init(void)</em></strong>, to initialize the <em>LSM6DS3</em> as described above.</li>

  </ul></li>

</ul>

In the next section of this lab, we will begin to utilize measurements being made by the accelerometer. To do so, you must first understand how to access accelerometer data for each of the three coordinate directions X, Y, and Z.

<table width="735">

 <tbody>

  <tr>

   <td width="412"><strong>5.</strong> <strong>PLOTTING REAL-TIME ACCELEROMETER DATA </strong></td>

  </tr>

 </tbody>

</table>

<ul>

 <li>Determine how to access accelerometer data from the <em>LSM6DS3</em>. Refer to the <em>LSM6DS3</em></li>

</ul>




In this section of the lab, you will create a “C” program, <strong>lab6_5.c</strong>, to plot accelerometer data for each of the three coordinate directions X, Y, and Z in real-time, using <em>SerialPlot</em>.

<em>SerialPlot</em> is a very simple open source tool that can be used to visualize serial data. In this course, we will utilize the USART system of the XMEGA, which is automatically translated to the USB protocol, to send data to the <em>SerialPlot</em> application on our computer. This will allow us to visualize many different “channels” of data very easily. For the purposes of this lab, each channel will represent one of the X, Y, or Z axes data from the <em>LSM6DS3.</em>

So, to simply communicate with <em>SerialPlot</em>, UART must be used to allow communication between your computer and your microcontroller, via the USB connection on your <em>µPAD</em>. Then, to plot accelerometer data from your IMU, all you must do is output the data via UART in the correct sequence of “frames” for it to be interpreted and displayed properly by <em>SerialPlot</em>.




<strong>Figure 3.</strong> Example <em>SerialPlot</em> Simple Binary Data Format

<em>SerialPlot</em> allows for a few different ways input data can be formatted. The first, most simple format is depicted in Figure 3. This format is known as “Simple Binary” in <em>SerialPlot</em>. There are a few things to note about this figure, and about the Simple Binary” format in general:

<ul>

 <li><em>SerialPlot</em> can plot up to 32 channels, so you must specify how many to use for your application. In Figure 3, three channels are used. This means in one “frame” of data, you must output three channels worth of data for it to work correctly.</li>

 <li><em>SerialPlot</em> can interpret most simple data types such as 8, 16, and 32-bit signed and unsigned integers, as well as floats. Figure 3 is an example of what 16-bit data would look like in this format. You can choose whether it is plotted as signed or unsigned.</li>

 <li><em>SerialPlot</em> can also support both little-endian and big-endian data formats.</li>

</ul>




For the same example given in Figure 3, assuming that three channels of signed 16-bit data in the little-endian data format were expected, the following three values should be supplied to <em>SerialPlot </em>in the same order given: 0x4305, 0x0020, and 0x3744. When outputting this data with UART, you would output the data in the order shown in Figure 3: 0x05, 0x43, 0x20, etc. After all six bytes have been sent to <em>SerialPlot</em> via USB, the plot will update with the three new values corresponding to each channel.




If you were using five channels of 8-bit unsigned data, you would only need to output five bytes, starting with the data that should correspond to channel one and ending with the data for channel five.




<ul>

 <li>Download and install Once installed, open the program and initialize each tab as follows:</li>

</ul>




Lab 6: Synchronous Communication Revision <strong>3</strong>




<ul>

 <li>For the <em>Port</em> tab, make sure your <em>µPAD</em> is connected to your computer and select the corresponding COM port. It should have “EDBG” in the name. Click the refresh button next to the “Port” field if it does not show up automatically. Choose the highest baud rate you can use for your given system clock frequency. Leave everything else as default, e.g., no parity, 8-bit data, 1 stop bit, no flow control. Wait to click <em>Open</em> until your program is running.</li>

 <li>For the <em>Data Format</em> tab, choose Simple Binary, three channels, int16 number type, and little-endian endianness.</li>

 <li>For the <em>Plot</em> tab, make sure that all three channels are visible, choose 1000 for the buffer size and plot width, make sure both check boxes are selected for “Index as X Axis” and “Auto Scale Y Axis”, and choose “Signed 16 bits” for the range preset.</li>

</ul>

Each of the three channels will be used to represent one of the X, Y, and Z axes measured by the accelerometer. For the purposes of this lab, <strong>make channel one display the X axis data, channel two display the Y axis data, and channel three display the Z axis data.</strong>

<strong>NOTE:</strong> Don’t forget to send the data in little-endian format (like you should have configured <em>SerialPlot</em> to accept), and that the data you are working with (from the accelerometer) is in a signed 16-bit format. This means that for every channel/axis, you need to output two bytes.

If you ever need to use the accelerometer data for arithmetic or logical comparisons, you should store it into <em>int16_t</em> variables, one per axis! This may be necessary for lab quizzes or hardware exams, so make sure you understand how to accomplish this.

<ul>

 <li>Create a “C” file, <strong>c</strong>. Write a “C” function to transmit a stream of sensor data via your USB Serial Port, in the correct order according to the <em>Simple Binary</em> format, following the pattern in Figure 3 and as described above. Make sure that your function transmits the correct number of bytes, in the right order. Your function should also utilize other functions (or macros) that you have already created, such as <strong><em>usartd0_out_char()</em></strong>. Be careful to <em>not</em> use <strong><em>usartd0_out_string()</em></strong>, since this routine expects a null-terminated string, which your data will likely not be! Instead, it is advised that you create a function, similar to <strong><em>usartd0_out_string()</em></strong>, that outputs <em>a specific number of bytes</em> via the relevant USART module, rather than continually output until a null character is encountered.</li>

</ul>




<h1>EXTRA CREDIT</h1>

Now, recall that the <em>LSM6DS3</em> will be configured to interrupt your microcontroller, upon completing an acceleration measurement. <strong>The routine that you will create below must only output data for <em>SerialPlot </em>when new data from the accelerometer becomes available, since we only care to plot new data. </strong>(There is no point in outputting the same data repeatedly.)

Moreover, as always, your program should spend as little time as possible within the respective interrupt service routine. In other words, your program must <em>not</em> output any data to <em>SerialPlot</em> within an ISR. Instead, a global flag (e.g., <strong><em>volatile uint8_t accel_flag</em></strong>) should be asserted to alert your main program that new accelerometer data is ready to be output.

5.6. Create a main routine within your “C” file, <strong>lab6_5.c</strong>, to plot accelerometer data for all three coordinate directions (X, Y, and Z) via the <em>Data Streamer</em>, as described above. <em>Remember to only transmit new accelerometer data.</em> As mentioned previously, configure the relevant USART module with the highest baud rate possible for your system clock frequency.

After your program is running, and after you have selected the appropriate COM port that corresponds to your <em>µPAD</em> within <em>SerialPlot</em>, click <em>Open</em> either [1] in the <em>Plot</em> tab or [2] at the top of the <em>SerialPlot</em> window.

<strong>NOTE: </strong>Recognize that Earth’s gravitational force vector (perpendicular to the ground) will continuously act on the accelerometer axes that are partially aligned with the gravity vector.

5.7. Using <em>SerialPlot</em>, plot all three axes of accelerometer data, in real-time. Take a screenshot of your graph, including all three waveforms.

<h2><u>PRE-LAB EXERCISES</u></h2>

<ol>

 <li>Why is it a better idea to modify global flag variables inside of ISRs instead of doing everything inside of them?</li>

</ol>

<ul>

 <li>To output two unsigned 32-bit values 0x12345678 [CH1] and 0x9A34F211 [CH2] to <em>SerialPlot</em>, list all the bytes in the order you would send them via UART.</li>

</ul>

<ol>

 <li>What is the most positive value that can be received from the accelerometer (in decimal)? What about the most negative?</li>

</ol>




For 10% extra credit, implement the same functionality for the gyroscope as you did for the accelerometer. There will be no PI help for extra credit.




<h2><u>PRE-LAB PROCEDURE SUMMARY</u></h2>

<ul>

 <li>Answer all pre-lab exercises, when appropriate.</li>

 <li>In § 1, create “C” functions to configure/utilize the SPI module that will connect to the <em>LSM6DS3</em></li>

 <li>In §§ 2 and 3, test the SPI “C” functions that you wrote in § 1, and create “C” functions to communicate with the <em>LSM6DS3</em> Take a relevant screenshot of the LSA, as described in § 2.7.</li>

 <li>In § 4, create a “C” function that initializes the built-in accelerometer within the <em>LSM6DS3</em>.</li>

</ul>

Electrical &amp; Computer Engineering Dept.             Lab 6: Synchronous Communication                               Christopher Crary, Instructor

Page 7/9                                                                                               Revision <strong>3</strong>                                                              Wesley Piard, Instructor

<ul>

 <li>In § 5, create a “C” program to plot accelerometer data for all three coordinate directions (X, Y, and Z), using <em>SerialPlot</em>. Take a screenshot of your plot, as described in § 5.7.      <strong> </strong></li>

</ul>

Electrical &amp; Computer Engineering Dept.             Lab 6: Synchronous Communication                               Christopher Crary, Instructor

Page 8/9                                                                                               Revision <strong>3</strong>                                                              Wesley Piard, Instructor

<table width="734">

 <tbody>

  <tr>

   <td width="220"><strong><u>APPENDIXES</u></strong></td>

   <td width="513"> </td>

  </tr>

  <tr>

   <td width="220"><strong>A.</strong></td>

   <td width="513"><strong>USING THE PROVIDED <em>LSM6DS3</em> C FILES </strong></td>

  </tr>

 </tbody>

</table>




Three files have been provided for you: <strong><em>lsm6ds3.c, lsm6ds3.h, </em></strong>and <strong><em>lsm6ds3_registers.h</em></strong>.

The <strong><em>lsm6ds3_registers.h</em></strong> file contains <strong>very</strong> useful definitions which give labels to the addresses and bitfields of the <em>LSM6DS3</em> internal registers, as shown in Figure 2.

The <strong><em>lsm6ds3.c</em></strong> file is blank and may be used as a place to <em>define</em> the <em>LSM6DS3</em>-related functions as described in § 3.

The <strong><em>lsm6ds3.h</em></strong> file is more interesting. First and foremost, it will serve as a place to put the <em>declarations</em> of the functions you <em>defined</em> in <strong><em>lsm6ds3.c</em></strong>. Additionally, it contains several type definitions which are provided for you to use. You are <strong>not</strong> required to use any of these type definitions; however, they will likely make your life a lot easier when it comes to managing the inertial data from the <em>LSM6DS3</em>. The following typedefs are provided:

<em>lsm6ds3_module_t – </em>An enumeration that can be used to specify a device (accelerometer or gyroscope) if you ever need to. An example would be a function that could act on either the accelerometer or the gyro. This enumerated type could be used as one of the argument types to the function.

<em>lsm6ds3_data_raw_t</em> – This type is a structure that can be used to store the data when read from the IMU. It supports both the accelerometer and the gyro, but you can use it exclusively for the accelerometer if you don’t choose to get the gyroscope working.

<em>lsm6ds3_data_full_t</em> – This type is a structure that can be used to access the full X, Y, or Z axis data in a signed 16-bit format. This is used in the following typedef.

<em>lsm6ds3_data_t</em> – This type is a union that will allow you to access the data read from the <em>LSM6DS3</em> in a much easier way. You will need to declare an instantiation of this union in your code, like so:




lsm6ds3_data_t lsm_data;

You are creating a variable of the <em>LSM6DS3_data_t</em> type, which is a union. You can choose the name of the variable; it doesn’t necessarily have to be `<em>lsm_data`</em>. Now, you should be able to store the <em>LSM6DS3</em> data directly into this union by accessing the “<em>LSM6DS3_data_raw_t”</em> member. Here is an example:  lsm_data.byte.accel_x_low = /* `lsm6ds3_read` call*/

After you read the rest of the accelerometer data in this fashion, you then have it all in a <em>contiguous </em>section of memory. Now, you can access <em>either</em> the bytes individually, <em>or</em> the full words that correspond to each axis’ data. For example, if you wanted to access the accelerometer’s Y-axis data, you would type the following:

lsm_data.word.accel_y

Notice the difference between this and the previous snippet. The same union (`<em>lsm_data`</em>), but different structure, is accessed. Because it is a union, these two structs share the same memory. When you loaded the individual bytes with all the IMU data, you filled the section of memory that corresponds to the union. This gives you the ability to either access the bytes individually, or the entire words!

Understanding how exactly unions and structures work isn’t necessarily in the scope of this class, especially if this is the first time you have used “C”. If you are interested in learning more, there is plenty of information online about these topics that you can learn more from.

Again, you do <em>not</em> have to use these constructs. They are just provided to introduce you to some more advanced functionality of “C”.

<table width="735">

 <tbody>

  <tr>

   <td width="733">Electrical &amp; Computer Engineering Dept.             Lab 6: Synchronous Communication                                        Christopher Crary, InstructorPage 9/9                                                                                                Revision <strong>3</strong>                                                                       Wesley Piard, Instructor<strong>B.</strong> <strong>TROUBLESHOOTING SERIAL PLOT </strong>The goal of this section is to help you with some of the issues students typically have with the serial plot software.<strong><u>Problem #1 (Flipped Low &amp; High Bytes)</u> </strong>v  Many students run into an issue where their data seems to fluctuate rapidly through a large range of values. This typically happens when the data from your <em>µPAD</em> is flipped. <em>SerialPlot</em> is taking in your low byte as a high byte and your high byte as the low byte. The simplest way to fix this is to restart you program and ensure that Serial Plot is running before you start the program on your <em>µPAD</em>.<strong><u>Problem #2 (Baud Rate)</u> </strong>v  If you are getting junk data from your <em>µPAD</em> on <em>SerialPlot</em>, this usually indicates that the Baud Rate is incorrectly input into <em>SerialPlot</em>. Check both your code and <em>SerialPlot</em> values to ensure that they match.<strong><u>Problem #3 (PuTTY)</u> </strong>v  If you are getting no data from your <em>µPAD</em>, ensure that you do not have PuTTY running. Only one program can have control over a serial connection of your <em>µPAD</em>, and PuTTY and <em>SerialPlot</em> don’t get along, so only use one at a time.<strong>C.</strong><strong>       </strong><strong>OPTIMIZATION LEVELS </strong></td>

  </tr>

 </tbody>

</table>

When using the “C” programming language within our course, you are required, unless told otherwise, to turn off the compiler optimization tool utilized by <em>Atmel Studio</em>. To do so, perform the following within <em>Atmel Studio</em>.

<ol>

 <li>Navigate to <em>“Project | &lt;project_name&gt; Properties”</em>.</li>

 <li>Select <em>“Toolchain”</em>.</li>

</ol>




<ol start="3">

 <li>Select the “<em>Optimization” </em>listing<em>, </em>located under `<em>AVR/GNU “C” Compiler`</em></li>

 <li>Set “<em>Optimization Level</em>” to “<strong><em>None (-O0)</em></strong>”.</li>

</ol>

For more details, read the last page of the document called

<a href="https://mil.ufl.edu/3744/docs/Create_Simulate_Emulate_Atmel.pdf"><em>Create, Simulate, and Emulate a Project</em></a> on the Software/Docs page of our class website.

<a href="#_ftnref1" name="_ftn1">[1]</a> An <em>accelerometer</em> is a device used to measure acceleration, e.g., static accelerations like gravity, or dynamic accelerations such as vibrations or movements in the X, Y, or Z coordinate axes. A <em>gyroscope</em> is a device that measures angular velocity and uses gravity to help determine orientation.