CODE PROGRESSION 

1) Generate scenarios
2) Generate data*
3) Run Simulations*
4) Analyze Results*
5) Merge Results**

*These steps can be run in batch by running the RUN ALL BATCHES commands below
**Only need to merge results if step 4 is run in batch

OPTIONAL
1) Analyze state space
2) Check sims/results***

***Only needs run if some jobs failed, the server crashed, etc.



##################################
### ENABLE WRAPPER PERMISSIONS ###
##################################

chmod +x Data_wrapper.sh
chmod +x Sims_wrapper.sh
chmod +x Single_sim_wrapper.sh

##########################
### GENERATE SCENARIOS ###
##########################

qsub -b y -V -cwd -j y -N julia -i Robust/Main/Scripts/Generate_Scenarios.jl -o Robust/Logs/Generate_Scenarios.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

#####################
### GENERATE DATA ###
#####################

qsub -b y -V -cwd -j y -N julia -i Robust/Main/Scripts/Generate_Data.jl -o Robust/Logs/Generate_Data.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

#######################
### ANALYZE RESULTS ###
#######################

qsub -b y -V -cwd -j y -N julia -i Robust/Main/Scripts/Analyze_Results.jl -o Robust/Logs/Analyze_Results.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

#####################
### MERGE RESULTS ###
#####################

qsub -b y -V -cwd -j y -N julia -i Robust/Main/Scripts/Merge_Results.jl -o Robust/Logs/Merge_Results.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia


###############################
### ANALYZE THE STATE SPACE ###
###############################

qsub -b y -V -cwd -j y -N julia -i Robust/Main/Scripts/Analyze_State_Space.jl -o Robust/Logs/Analyze_State_Space.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

################################
### MAKE ALL BATCHES OF DATA ###
################################

qsub -b y -V -cwd -j y -N julia -i Robust/TX-Green/Batch_Scripts/Write_Data.jl -o Robust/Logs/Write_Data_Batch.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

chmod +x Batch_Make_Data.sh 

~/Batch_Make_Data.sh 

######################################
### RUN ALL BATCHES OF SIMULATIONS ###
######################################

qsub -b y -V -cwd -j y -N julia -i Robust/TX-Green/Batch_Scripts/Write_Sims.jl -o Robust/Logs/Write_Sims_Batch.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

chmod +x Batch_Run_Sims.sh 

~/Batch_Run_Sims.sh 

##################################
### RUN ALL BATCHES OF RESULTS ###
##################################

qsub -b y -V -cwd -j y -N julia -i Robust/TX-Green/Batch_Scripts/Write_Results.jl -o Robust/Logs/Write_Results_Batch.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

chmod +x Batch_Analyze_Results.sh 

~/Batch_Analyze_Results.sh 

#####################
### CHECK RESULTS ###
#####################

qsub -b y -V -cwd -j y -N julia -i Robust/TX-Green/Batch_Scripts/Check_Sims.jl -o Robust/Logs/Check_Sims_Batch.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia




###############################################
############ SPECIFIC TO LLGRID ###############
###############################################



##############################################
### CHECK MEMORY REQUIREMENTS: RUNNING JOB ###
##############################################

qstat -j JOB_NUMBER |egrep maxvmem

###############################################
### CHECK MEMORY REQUIREMENTS: FINISHED JOB ###
###############################################

qacct -j JOB_NUMBER | egrep 'hostname|taskid|maxvmem' |awk '{print} /maxvmem/ {print "\n"}' ORS=" "

##############################################
### CHECK NUMBER OF JOBS CURRENTLY RUNNING ###
##############################################
qstat | grep -c ZA25454

###############################
### DELETE ALL RUNNING JOBS ###
###############################
qdel -u ZA25454

##############################################
### MODULES FOR LOADING/UNLOADING SOFTWARE ###
##############################################

export MODULEPATH=$MODULEPATH:$HOME/modules

module load julia-0.4.3
module load gurobi-6.5.0

module unload julia-0.4.3
module unload gurobi-6.5.0

#########################################
### CONFIGURE GIT FOR ADDING PACKAGES ###
#########################################

git config --global http.proxy http://llproxy.llan.ll.mit.edu:8080
git config --global https.proxy http://llproxy.llan.ll.mit.edu:8080
git config --global url."http://".insteadOf git://

###################################
### GENERIC COMMAND: RUN SCRIPT ###
###################################

qsub -b y -V -cwd -j y -N julia -i Script.jl -o Log.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

####################################
### GENERIC COMMAND: PERMISSIONS ###
####################################

chmod +x File.sh

####################################
### GENERIC COMMAND: RUN WRAPPER ###
####################################

qsub -N julia -o Logs.txt /home/gridsan/ZA25454/Wrapper.sh ARG1 ARG2 ARG3…

#######################
### RUN TEST SCRIPT ###
#######################

qsub -b y -V -cwd -j y -N julia -i TEST/Test.jl -o Test.txt /home/gridsan/groups/llgrid_beta/julia/0.4.3/bin/julia

