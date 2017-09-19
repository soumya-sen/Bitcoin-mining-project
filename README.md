# Bitcoin-mining-project
Using elixir
Distributed Systems [COP5612] - Fall 2017

Project 1 - Bitcoin mining

Project Members - Soumya Sen [UFID- 19217737] 
				  Subhrima Bhadury [UFID- 06988273]

CONTENTS OF THIS FILE

Introduction
Requirements
Installation and Configuration
Program flow
Project Questions

						INTRODUCTION

Our project has five different modules :- 

1) Server: This module uses GenServer and contains all the functions needed to run the program on the server side. Our server module calls the main GenServer in Elixir which is helpful in passing messages and helps in maintaining the state as well. In our case the initial state is the number of leading zeroes (k) which is entered the first time. The GenServer is initialized with this value.  

2) Worker: This module contains all the functions needed to mine bitcoins on the worker side. 

3) MainServer: This module contains the main function, parses the argument and decides the flow of execution, depending on the argument. This module also contains the findIP() function which discovers the IP address of the server that this program is running on.

4) Hashing: This module contains the bitcoinHashing function which is responsible for actually generating the bitcoins. We use 'HashCash algorithm' by appending a counter at the end of the randomized string starting from zero. This counter is incremented if the SHA256 hash obtained does not have the 'k' number of leading zeroes. This is carried out in an infinite loop. 

5) RandomizeString: This module contains the functionality to generate random strings. Our character set to randomize strings is a-z, A-Z and 0-9. The server directly calls the randomize() function of this module to generate the random string. The worker receives the random string from the server.

NOTE: 
-At the time of boot, before running the program we need to run the 'epmd -daemon' command on the terminal so that the the epmd daemon is detached. The epmd daemon is the Erlang Port Mapper Daemon.
-We tested the findIP function for getting the server's IP on Storm server, Windows and MAC OS. This function successfully retrieves the IP address of the server in all three cases. Sometimes for Linux, the port keeps changing, so this function might not work in that case. We might have to hard code the IP address of the server in the code to run it.
-Before running the worker with the IP address of the server, we have to run the Server by giving the numeric k value first (i.e the number of leading zeroes) and then run the worker. Otherwise the worker will not be able to connect to the Server. 

						REQUIREMENTS

The following need to be installed to run the project:

Elixir 1.5.1
Virtual Studio Code 1.16.0

				INSTALLATION AND CONFIGURATION

First we create a new mix project by running the command 'mix new project1' on the terminal. This creates a new mix project with the respective lib and config folders along with the mix.exs file. Here, we need to make the following changes to the mix.exs file- We add escript: [main_module: MainServer]] in the definition of project in the mix file so that the main module can be loaded successfully. In our project, the main module is MainServer. 
After saving the mix.exs file, we can run our project by ./project1 [args]
In our case, if the [args] is a number (e.g k = 3), then the Server is mining bitcoins with that many leading zeroes (3 in this case). If the [args] is an IP address, then it is the worker which is asking to participate in the bitcoin mining process. The first call message to the Server returns the value of k to the worker. The second call to the server sends an appended randomized string to the worker, generated by the server. The worker mines bitcoins with these randomized strings and sends it to the server using the 'cast' function if the leading number of zeroes matches the value of k. This is carried out in a loop and every time the worker calls the server to obtain a randomized string.

					PROGRAM FLOW

First the server runs by mining the bitcoins which match the same number of leading zeroes as 'k'. Bitcoins are mined by performing a SHA-256 of a random string which has been appended to our UFID and this process should run infinitely. We can also call the process asynchronously on the server by spawning several processes in the server. 
When the bitcoin mining process is running on the main server, the workers can extend and help the server to mine the bitcoins. In our project the Server provides the initial 'k' value, which is the number of leading zeroes in the bitcoin. It also sends a randomized string to the worker every time it calls the server. The worker and server both mine bitcoins by spawning several processes. We have 10,000 processes right now for both the worker and server in our program. In our project bitcoins are mined by using the 'HashCash Algorithm'. A counter is appended to the randomized string and the resulting string is hashed. If the hash has 'k' leading zeroes, then this is a valid bitcoin. Otherwise, the counter is incremented by one. This counter is initialized to 0 and it keeps getting incremented every time until a bitcoin with 'k' leading zeroes is mined. If the server mines this bitcoin, then it gets printed directly. If a worker mines this bitcoin, then it is sent to the GenServer, who then proceeds to print it on the server. If the server dies and the program terminates on the server, the worker keeps calling the server unknowingly. We have handled this exception by exiting gracefully. The worker gets a message which says 'Server has died' and thus terminates.

					PROJECT QUESTIONS 

1. Size of the work unit that you determined results in best performance for your implementation and an explanation on how you determined it. Size of the work unit refers to the number of sub-problems that a worker gets in a single request from the boss.

In our project, the GenServer is started when the server starts mining bitcoins. Then, the workers can connect to the Server's IP and help in mining the coins and send them to the GenServer. The server provides the appended value (UFID which has to be attached) and also generated a Random string for the client of length 10. In the random string, we have a-z, A-Z and 0-9 as our characters.  As we have 62 distinct characters, the total number of possibilities is 62^10 ≃ 8.4×1017. As we're sampling uniformly from this set, and we have 10^6 unique values already, the probability of a collision is 10^6/62^10 ≃ 1.2×10^−12. The possibility of a collision is about one in a trillion. Also, after creating a random string from the server, we use the hashcash algorithm as a proof of work in bitcoin mining. So, the worker or the server won't be mining the same bitcoins.

We ran our project with 1000, 10000 and 100000 work units. We observed that 10000 work units provided the best performance. Our program works with First come first serve (FCFS) of the workers. When the workers are running simultaneously with the server, the worker which gets the bitcoin first reports it to the server but our program works in such a way that the workers get connected to the server in the manner of FCFS. The workers get the value of k and the random string of length 10 from the boss in our project. 

2. The result of running your program for ./project1 4

In our project, soumyasen:kjsdfk [which is the UFL id and a random string] is appended to the randomized string generated by the server of length 10. In the first output the random string is sgm5dLEf3M and then using the hashcash algorithm we have discovered the bitcoin with 4 leading zeroes. In the first output the counter at which we get the first hash with 4 leading zeroes is 1012. After separating it with a tab, the SHA-256 of the string with 4 leading zeroes is printed, which is the mined bitcoin. As we are spawning the process infinitely, the program doesn't end till we explicitly terminate it. So, we are providing some of the outputs printed on the console when we run ./project1 4. 
		

			After running this on the storm server 

soumyasen;kjsdfksgm5dLEf3M1012	00007d509f1ea1f29db65a237d1cc46198c72daafb8822621a87a88339ce3133
soumyasen;kjsdfkbyxsaDQ0aS2543	00006445d9ab6ce0148b52e88478fa3cf6f84f4486135345e969e600666c54d8
soumyasen;kjsdfkYAy4FtiKkt4480	0000deada2ffe4235676a20de674b48a6d983d780f88124d52ff178620fc68f9
soumyasen;kjsdfk5GaDruHVhr2995	0000fc67863619c52d51c2eaa0a847da48a785773e8e8f155a34536542e97af4
soumyasen;kjsdfk3SHD7rT4hJ4406	00000e159146299d05eb63fdc8fa982a30cec983d42acb35ff2abe3588c753f1
soumyasen;kjsdfkTmOreebdVg2684	0000421f019a1a8191c3187adde4d809439357537265cfdc2a175ab5119289ad
soumyasen;kjsdfk8FXZUmIUja4653	00009ff3b5bd5464e1639b6d25d327c43c6b744694c2aedb5c336c9ce92eb633
soumyasen;kjsdfkpYhsJnGdUl1279	0000971d7026fa4b71632eedb8e39d4c3ea2bd07498cffc800c69ee45ceb9348
soumyasen;kjsdfkSs0AbWzX1z6145	000024a0a7054f6d3cb6e464fba53b63d7f82036e3d0000aa01eea14299b4b02
soumyasen;kjsdfkiJey3i4xkf5754	000060aebf2104e6bd0bdcb2d8676005d94caa03f81ddf07d3b7d63d1c128ca9
soumyasen;kjsdfkpYhsJnGdUl2372	0000eabc546fe7f0c0a70174b35c7ab631a7bd9fb152ad46639f94a0c493ff0f
soumyasen;kjsdfk6vRKba0wWm2054	0000938a3afe61e74552b297dc123b89d507a956b86b4adfd8c914946c44d6ea
soumyasen;kjsdfkE5agkxuYU6152	000029a9888dbb3240b4a8946debf1e61d587a4264bd6a394dfaf02046ec73e6
soumyasen;kjsdfkP3PcM45fND2389	0000e637d0b4e0ad6a82dfbef622fe27e3904824d529e305c7b8e54c4ac18b3e
soumyasen;kjsdfkLQViv8IErZ7401	0000b334b19497d8ed3f34246e645c9bd378aa089bf043522cc5b7cf5671eba8
soumyasen;kjsdfkA0MNeyOE244664	0000c5bc6aac0785457b633d72240b316531a205358d9e541302da813c0acc9f
soumyasen;kjsdfkDqOYuFp2Pg4482	0000023792f82e6d5f6ae8b925422d50f82d17ea0169b7f78097500e1ac1152d
soumyasen;kjsdfki3SP4QVuvD7536	0000ebe4b586b5d23f55e6ab5689eadd2badb189c64a4d913938deb7b8342fa4
soumyasen;kjsdfkIi3mGFBWrN8009	0000ae8585f0730fd1f2928bfc2726e4caf30c262f43dbac25fe618d0bdab743
soumyasen;kjsdfkOueGH0J0ew7767	0000d17efb032d09e213cf1785a8aa5d98ffc30dbd0f86717761fef760bc1005
soumyasen;kjsdfk3FMwAorrF66629	0000dd30a1eb2d002fec8ab143090a9b3e0494906156567fd49c00608ed7c8da

3. The running time for the above as reported by time for the above, i.e. run time ./project1 5 and report the time. The ratio of CPU time to REAL TIME tells you how many cores were effectively used in the computation. If your are close to 1 you have almost no parallelism (points will be subtracted).
time ./project1 5

From this figure, we see that in Mac the activity Monitor shows our beam.smp is utilizing 376.4% of the CPU and the CPU usage is using all the 4 cores.

soumyasen;kjsdfkVV04PKaSU82938	000009872f00c9928871145116d3b8d330e240bf5b37cd07273595c32cc5f605
soumyasen;kjsdfkvmHcIwt5AA3395	00000aa74cb7b629b15bbc93050a15997c1df4bbf402118a7eea571aa9d5ed4e
soumyasen;kjsdfkxl07rbOWQL10259	000005f73bcab588047291e39ab982dc4e1465c2b6c8d3013d2dea6d1d80d398
soumyasen;kjsdfkLdBLQQhCTC21576	000003fd570086a5db20e71be2451ae0cca66ac3c3647101437e1eccd2440458
soumyasen;kjsdfkw5o2oBZATh1073	00000c5da782a68e7c356e8b1f881fe861b283d19447904530b297b28ff60f64
soumyasen;kjsdfkpz5MA1EvQj24641	00000115aa074b81a0fb6cf9035886a0f275934adae0fdd0673647f4c306d695
soumyasen;kjsdfknENALD9TrX20007	00000334713ccc885cdb67499e59e4e731e63db70a02a7a526e5cc3dd9493ade
soumyasen;kjsdfkZhthxWouNr20238	00000943f232f58563ff3420a36634fa2d561150ae103d7d0c8926ada6a8de73
soumyasen;kjsdfkD5Zxp2dhQ227915	00000b457465916daabad086134ffcb7cf6f567a887587b2f53dd85de12e1d6b
soumyasen;kjsdfkG5LkP2CFaX30082	00000abe5b88294a60e2839ac7c5405a32d24bcb0709ca4578c4ac9eefb3e157
soumyasen;kjsdfkWGBZfi4dhl6441	0000097a336c4ad9da9e44914080c86aa7aadd35b46b69b0112f436448685292
soumyasen;kjsdfksr3zNEj65727820	000005d4ec74dd68419ea91e53f22465a7bafb3ba1c35ca06b2ee9f9524b299d
soumyasen;kjsdfkqPrHyrxdiD35187	00000365b2b06ee3798a3cd81795e7aac6cff78ed2439a66b9335e52acaedbf5
soumyasen;kjsdfkou7ZROLJn911202	000003ff0e842f8ea4200b8895f377a1742bf3999d583b02ec33f315ebedf626
soumyasen;kjsdfkyy2nWseze924533	00000a686c6dc46d16402f2d3804c24ad846062576419949467393e294a645f4
soumyasen;kjsdfkG8gsPIfYAc6663	00000f83ee0c5b788d8d438a77060ca15f47e6787a835a33caaf6195207ade79
soumyasen;kjsdfkafMXyDcWm618838	00000f373936ca67ab16c747f03c037e4d6b3cb263b964f61ca031cd59f91e4f

real	1m54.744s
user	6m59.298s
sys	0m5.732s

CPU time : - (User time + Sys time)/(Real time) = 425.03/114.744 = 3.704 

We ran our programs on MAC OS and then ran the command top and also checked our activity monitor to see the CPU utilization. We see that the top command and the Activity monitor showed our CPU utilization in the range of 350-380. The average CPU utilization was around 360 according to our activity monitor on a quad-core machine. 

4. The coin with the most 0s you managed to find.




This was the number of coins we got after running the project with 7 leading zeroes. We got a bitcoin with 8 leading zeroes as well. 



./project1 8

subhrima;kjsdfk2dCoro6VMu354165 000000004f91fddf6ca067d5d6ef6a785c95f27addc1d0847a80a7b209431345

The maximum number of leading zeroes that we found in a bitcoin was 8. This was found when we connected one worker with the main server.

5. The largest number of working machines you were able to run your code with.

We tried running out project with 4 working machines to mine the bitcoins on the server. Due to the lack of resource, we ran our code with just 4 working machines. One was the main server and the other three machines were the worker machines asking for work from the main server. 



