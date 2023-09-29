# Computing tricks

<!--BEGIN TOC-->
## Table of Contents
1. [Persistent jobs](#persistent-jobs)
2. [Running jobs in parallel](#running-jobs-in-parallel)

<!--END TOC-->

## Persistent jobs <a id="toc-tag-mdtoc" name="persistent-jobs"></a>

To keep jobs running, even after you've logged off, use the "no hangup" command

```bash
nohup ./python my_8_hour_script.py &
```

## Running jobs in parallel <a id="toc-tag-mdtoc" name="running-jobs-in-parallel"></a>

> THIS IS WHY WE NEED A SCHEDULER -Fergus

To run jobs in parallel with bash you can run a for-loop with the number of iterations needed and add the character `&` to the end of your line.

For example, here I wanted to run the same task 500 times so I ran 25 batches of 20 concurrent tasks

```bash
for i in {1..25}; do
	echo -e "\nROUND $i\n"
	for j in {1..20}; do
		python ./main.py ./output/"${i}"_"${j}".fits &
	done
	wait
done
```

The `wait` command ensure that all 20 code runs in the batch are finished before beginning the next batch (**Don't forget it**).

Note: Do check the current load on the server and consider how many cores / memory to take up. If you use up too much there will be insufficient memory for you to log back in and cancel the job (apparently)

_Request_: please don't do this often. And writing a shell script where you forget to put `wait` is too likely! Maybe a better thing to do here is use `xargs` to throttle the number of consecutive jobs (e.g. maximum 10 jobs simultaneously)

```bash
cat parameters.txt | xargs --max-args=1 --max-procs=10 python ./main.py
```

Each line of `parameters.txt` contains your `./output/"${i}"_"${j}".fits`. This _can_ be generated in the above loop

```bash
echo "" > parameters.txt # clear the current file
for i in {1..25}; do
	for j in {1..20}; do
		echo "./output/${i}_${j}.fits" >> parameters.txt
	done
done
```
