# Query2Tree
all_in_one.py
This all-in-one Python script automates the setup and execution of a query to revolution tree using GTDB databases pipeline on a fresh Ubuntu server. It does the following:

Installs essential packages and tools:

tmux (for session management),
pigz (multi-threaded gzip),
mmseqs2 (alignment/search),
A user-specified MAFFT .deb file (e.g. mafft_7.526-1_amd64.deb),
trimAl (compiling from source .zip),
IQ-TREE2 from a .tar.gz.
Downloads and prepares the GTDB dataset from official links, if you set BUILD_GTDB = True.

Decompresses archaea & bacteria .faa.gz.
Parses each .faa file to build a contig → top-level ID mapping (e.g., DAUV01000075.1_1 → GB_GCA_002505325.1), so we can later rename contig-based IDs in our final results.
Creates an mmseqs2 DB from input.fasta, then optionally runs a local search vs. the GTDB DB.

Renames headings in the final mmseqs2 alignment FASTA:

Takes contig-based IDs (like DAUV01000075.1_1) and maps them to top-level IDs (like GB_GCA_002505325.1),
Appends _{number} if multiple hits map to the same top-level ID, ensuring unique headings for downstream phylogenetics.
Aligns the final FASTA with MAFFT (--globalpair), trims with trimAl (-automated1), optionally renames headings a second time if duplicates appear after trimming.

Runs IQ-TREE2 with the final FASTA file, using:


iqtree2 -s final.fasta -B 1000 -alrt 1000 -T THREADS
to perform ultrafast bootstrap and SH-aLRT tests in a multi-threaded way.

IQ-TREE Checkpoint: If the process stops, a .ckp.gz file is created. Re-running the same iqtree2 command resumes from that checkpoint.

Requirements
Ubuntu (or a similar Debian-based system).
Sufficient privileges to install packages (sudo).
The following user-provided files in /home/ubuntu:
mafft_7.526-1_amd64.deb (or whichever MAFFT .deb).
trimal-1.5.0.zip (or whichever trimAl source .zip).
iqtree-2.3.6-Linux-intel.tar.gz (or whichever IQ-TREE2 .tar.gz).
(Optional) input.fasta if you plan to do the final alignment + tree steps.
Internet access to download the GTDB dataset if BUILD_GTDB = True.
Usage
Copy all_in_one.py to /home/ubuntu on your server.
Adjust parameters at the top if necessary:
THREADS for multi-threading,
MAFFT_DEB_FILENAME, TRIMAL_ZIP_FILENAME, IQTREE_TAR_FILENAME,
BUILD_GTDB = True (if you want to build the GTDB DB),
RENAME_DUPLICATES = True (if you want to rename duplicates post-trimAl).
Make it executable:
chmod +x all_in_one.py
(Recommended) Start a tmux session to keep processes running even if disconnected:

sudo apt update -y && sudo apt install -y tmux
tmux new -s mysession
Run:

./all_in_one.py
Watch it install and configure everything. When done, you will have:
Tools installed,
GTDB data downloaded (if BUILD_GTDB=True),
Potential mmseqs2 DB built from input.fasta,
A final FASTA with renamed headings,
A tree from IQ-TREE2 with 1,000 ultrafast bootstraps and 1,000 SH-aLRT replicates.
Script Flow
System Prep:
sudo apt update, install tmux, pigz.
Install Tools:
mmseqs2 from wget https://mmseqs.com/latest/mmseqs-linux-avx2.tar.gz.
MAFFT from your local .deb.
trimAl compiled from source .zip.
IQ-TREE2 from .tar.gz.
Download GTDB (BUILD_GTDB = True):
gtdb_proteins_aa_reps.tar.gz, bac120_taxonomy.tsv, ar53_taxonomy.tsv from official links.
Decompress archaea & bacteria .faa.gz.
Parse each .faa file to build a dictionary: contig IDs → top-level ID. Store it in a local TSV (contig_map.tsv).
Build (or partially build) an mmseqs2 GTDB DB.
Create DB from your input.fasta, optionally search vs. the GTDB DB.
Rename mmseqs2 results from contigs → top-level IDs, adding _2 if duplicates.
Align with MAFFT, trim with trimAl.
Optionally rename headings again if more duplicates appear.
Run IQ-TREE2 with -B 1000 -alrt 1000 -T <THREADS> on final FASTA.
IQ-TREE Checkpoint Notice
If IQ-TREE is interrupted, it writes a .ckp.gz file. To resume:

Start the same environment,
Copy the .ckp.gz, the alignment, and any other input files,
Re-run the exact same IQ-TREE command.
IQ-TREE detects the checkpoint and resumes from where it left off.
Additional Notes
THREADS is used for pigz, mmseqs2, MAFFT, and IQ-TREE2. Adjust it for your CPU availability.
The script does not unify .faa sequences in the database itself—only renames headings in the final mmseqs2 alignment output.
If you skip building GTDB (BUILD_GTDB=False), you can still do local steps for alignment & tree building with your input.fasta.
License
This code is provided as-is without warranty, under an open license. Feel free to modify or adapt it for your Argonaute/GTD-based pipeline. Use at your own risk and have fun analyzing prokaryotic Argonaute proteins!
