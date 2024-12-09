# CA_Bonus M123040116
Implementation paper: **The Breakthrough Memory Solutions for Improved Performance on LLM Inference**
<br>This is the link of my implementation Video Demo:
https://drive.google.com/file/d/1LgOjpNlGhjwFtobbR7meHiMovevG2Q09/view?usp=sharing
<br>Implementation Document PDF will be attached in this repository: [M123040116 CA PDF](CA_Bonus/CA_M123040116%20Implementation.pdf)

<h1>Introduction</h1>
In this implementation, a PIM Simulator was used to conduct some tests referenced in the paper. Simulations were performed based on the content of the document; however, various physical implementations could not be demonstrated due to the lack of essential tools, such as Samsung's AquaBolt-XL, a CXL-Memory controller, and several other tools mentioned in the paper. Therefore, efforts were made to carry out the work using the resources available and the online PIM simulator. An attempt was made to simulate the functionalities and behaviors discussed in the paper to the best extent possible. This approach allowed for a partial validation, even if the full physical implementation could not be realized.
 <br>
<h2>PIM Simulator</h2>
PIMSimulator is a cycle accurate model that Single Instruction, Multiple Data (SIMD) execution units that uses the bank-level parallelism in PIM Block to boost performance that would have otherwise used multiple times of bandwidth from simultaneous access of all bank. The simulator include memory and have embedded within it a PIM block, which consist of programmable command registers, general purpose register files and execution units.
This Simulator was developed by Samsung Advanced Institute of Technology (SAIT) and includes PIM Block which supports various instruction sets as ALU operations (ADD, MUL, MAC, etc..). A PIM Kernel which it is use to generate a set of memory transactions for enabling PIM operations and HBM2 support.
For the installation of the PIM Simulator, it is required to run it in a Linux environment, in this test, Ubuntu Linux was used.
<h1>Installation</h1>
1.	Download or clone the following repository into your pc through linux terminal: https://github.com/SAITPublic/PIMSimulator/tree/dev?tab=readme-ov-file#4-programming-guide<br>
<br>2.	Once on Linux Environment, run this following command in the terminal for compiling the Simulator:
<br>sudo apt install scons<br>
<br>3.	Proceed to run this command for the tests: 
<br>sudo apt install libgtest-dev <br>
<br>4.	Once done, install the PIM simulator (Keep in mind, these processes are done inside the PIMSimulator folder): 
Scons <br>
<br>5.	Now, were done, proceed to run the following command to test if it works:
<br># Running: functionality test (GEMV) <br>
./sim --gtest_filter=PIMKernelFixture.gemv <br>

![image](https://github.com/user-attachments/assets/a2173118-c0a3-46c8-a776-ae62067f4eb6)
6.	Now, to test the performance of the GEMV, we run this command: 
<br>./sim --gtest_filter=PIMBenchFixture.gemv
![image](https://github.com/user-attachments/assets/bd56a197-8f81-49ce-badc-e35188dea869)

<h1>Solutions/ Implementations mentioned in the paper</h1>
As one solution mentioned in the paper “To address the memory wall problem, various architectural approaches have been proposed. The solutions involve increasing the number of pins (input–output [I/O]) or raising the I/O frequency in the system”. The data was modify to these value:

![image](https://github.com/user-attachments/assets/ec7f2049-3cf8-49fe-8978-4f0e6fbe6feb)

Which simulating it, would give us this result:
![image](https://github.com/user-attachments/assets/98f22eed-c550-4a51-b1b0-87e8b91efff0)

Cycle of 160335 with a speed-up of 2.74.
Printing the Bandwidth (GB/s). we would get this as a result:
![image](https://github.com/user-attachments/assets/31de664a-ad82-4943-9e5c-676ef053c617)

Now, to simulate or demonstrate this part of the paper “The solutions involve increasing the number of pins (input–output [I/O]) or raising the I/O frequency in the system”. To perform this simulation, it was through increasing the PIN JEDEC_DATA_BUS_BITS value from 64(Default) to 128. As Increasing this value simulates increasing the number of I/O pins.    
![image](https://github.com/user-attachments/assets/7a51347c-dcd0-48ec-8857-f8b48059ea97)

And the number of frequencies of tCK to 1.25 as reducing tCK increases the frequency, thereby boosting I/O throughput.
  ![image](https://github.com/user-attachments/assets/327f94de-6aaa-493e-9e9f-a76906251b72)

So, the changes made in the system was PIN JEDEC_DATA_BUS_BITS (I/O Pins (Bandwidth) and tCK (I/O) Frequency 
Simulating the test, would give us the output:
  ![image](https://github.com/user-attachments/assets/9a6cb91d-0af0-4485-b4ec-5859dc3c30b2)

We can see that it performs better than the default values.

|                         | Default Values (Default Bandwidth, Frequency) | Increased I/O (for bandwidth and frequency) |
|-------------------------|-----------------------------------------------|---------------------------------------------|
| Cycle                  | 160335                                        | 80748                                       |
| I/O Bandwidth (GB/s)   | 1.28e-07                                      | 1.024e-07                                   |
| Speed-up               | 2.7421                                        | 1.34902                                     |


 
So, we can conclude that by increasing the number of pins or raising the I/O frequency in the system can be a solution for memory wall problem as mentioned in the paper. Now following with another solution mentioned, according to what is it mentioned in the pdf “While we have demonstrated the performance and energy characteristics using one PIM block for every two banks, it is also possible to deploy one PIM block for each bank. In such a scenario, there is the potential to double the internal memory bandwidth, which means that the PIM can achieve twice the processing power than before” this refers that Deploying one PIM block per memory bank, instead of one for every two banks, could double the internal memory bandwidth, allowing the PIM to achieve twice the processing power.
For this part, we modify the file PIMBenchTestCases.cpp by adding two tests, one with one_pim_per_bank and one_pim_per_two_banks to prove that we can achieve twice the processing power with one pim per bank

![image](https://github.com/user-attachments/assets/7156c895-4e1e-4aaa-a182-cabd77ff9861)

In the one_pim_per_bank and one_pim_per_two_banks running at a speed of 2.0. <br>
![image](https://github.com/user-attachments/assets/e86290f4-b595-4d06-bef9-c60fa52319d1)

Number of banks for one_pim_per_bank will be 128 <br>
![image](https://github.com/user-attachments/assets/a5b22228-b5dd-43d0-a0d5-a128fa7f9d52)

Number of banks for one_pim_per_two_banks will be 64 since its one pim per 2 banks=128
 
**Result for ONE_PIM_PER TWO_BANKS**

![image](https://github.com/user-attachments/assets/3e748b51-fc22-4cc9-aa1f-be00261c7fb2)


Using NUM_BANKS=64. Which is 128/2= 64 (Per two banks), we get a speed-up of 2.73138
 
**Result for ONE_PIM_PER_BANK**
![image](https://github.com/user-attachments/assets/3c4d22b4-0a21-4e2d-ae8c-8de8c84b1490)

Using NUM_BANKS=128 (Default) gives us a speed up of 4.7012
In summary, we can see the one_pim_per_bank is faster over the one_pim_per_two_banks as mention in the paper. Meaning the enhanced system (PIM-enabled for ONE_PIM_PER_BANK) runs 4.7012 times faster than the baseline while ONE_PIM_PER_TWO_BANKS only runs 2.70 faster than the baseline meaning that one_pim_per_bank is faster over the one_pim_per_two_banks as mentioned above.
 
<h1>Conclusion</h1>
In conclusion, the use of this tool PIM Simulator provided a opportunity to and demonstrate some of the solutions mentioned in the paper. However, due to the tools that were used in the real-world implementation such as Samsung's AquaBolt-XL, a CXL-Memory controller, and other key components, it was not possible to implement all solutions mentioned in the paper due to materials and tools used as mentioned above.
