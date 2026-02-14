# Rust_on_FPGA

This project records how to implement a RUST project on FPGA.

# Build BOOT file

I would like to skip this stage cause you may find reference in vitis official portal which gives a detailed guidance.

# Install RUST environment on your PC (In example of Ubuntu OS

  command:
  
      curl  https://sh.rustup.rs -sSf | sh

  check verision:

      cargo --version
      
      rustc --version

  Install any tools/library required.
  
# Make sure Petalinux can access the ethernet.

  1. connect ethernet wier between your PC and VCK190. (On FPGA, choose the ethernet port near the power botton, upper one is eth0, down is eth1.
  2. On PC side, set a statistic address for FPGA, and on FPGA side, connect to the addr after setting addr,mask and gateway. ( I would like to sugest GPT for details

# Setup Petalinux 
  Once Petalinux have access to Internet, install some necessary tools, in case of VCK190， I command:

         dnf install curl

         dnf install gcc
  
  installed whatever you need, difference is that it use dnf rather than apt on Ubuntu.

# Cross-compile executable rust file
 You can choose to install RUST environment on Petalinux, but usually a more efficient way is to cross-compile on your PC (mine is Ubuntu), and scp your executable file to the petalinux, to do that, you first install the target platform which execut the project, so on your PC:

      rustup target add aarch64-unknown-linux-gnu

      sudo apt-get install gcc-aarch64-linux-gnu

then change the file config.toml by adding a target:(I take binius64 project as an example, whose compilation file locates in Binius64-main/.cargo, this is their github link:https://github.com/binius-zk/binius64?tab=readme-ov-file )

      [target.aarch64-unknown-linux-gnu]
      linker = "aarch64-linux-gnu-gcc"
    
then compile with:

        cargo build --release --target aarch64-unknow-linux-gnu -p binius-examples --example keccak  ( as an apple-to-apple comparison, command on PC is "cargo run --release --package binius-examples --example keccak"

after compilation, you should find a executable file in DIR:  

    DIR: Binius64-main/target/aarch64-unknown-linus-gnu/release/examples

then scp your file to the Petalinux with

    scp -O your.file petalinux@your-addr-ip:/home/petalinux/

If you dont know Petalinux addr ip, check with 

        ifconfig -a

you will find your ip address in port eth0.

# Execute
Go to the DIR and run the executable file, for my case, that's called keccak

      cd /home/petalinux/

      chmod +x keccak

      ./keccak

# Any possible issue
Maybe I ignore some steps because it's 11 pm and I'm typing these words with my tired mind in case I forget it tmr. Pls go easy with this tutorial and feel free to reach out to me if you find any issue.  
