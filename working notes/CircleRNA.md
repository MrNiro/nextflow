# Set up Nextflow & nf-core/circrna on Linux(AWS)

## Minimum requirement
- vCPUs: 2
- memory: 32 GB
    - more than 64 GB would be better
- Recommended Instance type on AWS: ```r5a.2xlarge```
    - vCPUs: 8
    - memory: 64 GB
    - pricing: about 0.44 USD per hour


## scripts

- set up basic env
    ```
    sudo apt-get update
    sudo apt-get install default-jdk git wget docker.io -y
    ```

- install nextflow
    ```
    cd ~
    wget -qO- https://get.nextflow.io | bash
    chmod +x nextflow
    sudo mv ./nextflow /usr/bin/
    ```

- enable docker and pull nfcore/circrna
    ```
    sudo chmod 666 /var/run/docker.sock

    # in case docker not started
    sudo dockerd

    sudo docker pull nfcore/circrna:dev
    ```

- set to use DSL 1 & python alias
    - this step is not always necessary
    - try this only if you encounter errors related to "into" operator
        - 'into' operator is not available in DSL2
    ```
    export NXF_DEFAULT_DSL=1

    alias python=python3
    ```

## start nextflow
    ```
    git clone https://github.com/MrNiro/circrna.git
    sudo chmod 666 /var/run/docker.sock
    nextflow -bg run main.nf -profile docker,sample -resume &
    ```

## Upload results
    ```
    aws --profile=[user_name] s3 sync ./pipeline_info s3://[bucket_name]
    ```


## Problems
- Pipeline failed sometimes:
    - caused by error: ```SIGHUP```
    - Can be resumed if re-run the pipeline with ```-resume``` option


## Dependencies to be installed without Docker
```
sudo apt install python3-pip r-base-core samtools hisat2 fastqc rna-star stringtie bedtools -y

wget http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64.v385/gtfToGenePred
chmod +x gtfToGenePred
export PATH=$PATH:/home/[user-name]/gtfToGenePred
# alias gtfToGenePred=/home/niro/gtfToGenePred

sudo pip install multiqc circexplorer2
```