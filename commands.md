# General Tips/Resources 
- MAFFT documentation: [https://mafft.cbrc.jp/alignment/software/](https://www.ebi.ac.uk/seqdb/confluence/display/JDSAT/MAFFT+Help+and+Documentation)
- IQ-TREE 2: [http://www.iqtree.org/](http://www.iqtree.org/doc/)
- TreeTime:
  - Documentation: [https://treetime.readthedocs.io/en/latest/installation.html](https://treetime.readthedocs.io/en/latest/)
  - GitHub: https://github.com/neherlab/treetime

# Step 0: Preparation
- Download MAFFT: https://mafft.cbrc.jp/alignment/software/
- Download IQ-TREE 2: http://www.iqtree.org/
  - Download from site. Unzip. Open the bin folder. Move the executable file to relevant directory. Called iqtree. Icon looks like black rectangle, says exec in neon green letters in top left of icon.
- Install TreeTime: https://treetime.readthedocs.io/en/latest/installation.html
  - Choose one method to install
  - Install from PyPi: `pip install phylo-treetime`
  - Install from Conda: `conda install -c bioconda treetime`
  - Install from Source:
    -`git clone https://github.com/neherlab/treetime.git`
    - `cd treetime`
    - `pip install .` **make note of:** the space between install and .

# Step 1: Perform Multiple Sequence Alignment using MAFFT
- **Input:** Input sequence FASTA file (X.fas)
- **Output:** Output alignment FASTA file (X.aln)

## Indvidual Command
`mafft --thread <THREADS> --nuc --auto <SEQUENCES.FAS> > <ALIGNMENT.ALN>`
- `<THREADS>`: How many threads to use
  - Use -1 to use an appropriate amount of threads based on user's computer. Will automatically count the number of cores in user's computer and use that number of threads
  - Default: 1 if not specified
  - The number of threads must be less than or equal to the number of total physical cores in user's computer
  - https://mafft.cbrc.jp/alignment/software/multithreading.html
- `<SEQUENCES.FAS>`: Input sequence file (FASTA)
  - Unaligned sequence
- `>` input.fas > output.fas
  - Delineates input and output 
- `<ALIGNMENT.ALN>`: Output alignment file (FASTA)
  - Has gaps added
  - This output alignment FASTA file will be one of the inputs to Step 3. 
 
# Step 2: Perform Phylogenetic Inference using IQ-TREE 2
> [!Warning]
> Plug your latop in! This command will take longer than previous command to run (few minutes) and will drain your battery. 

- **Input:** Input lignment file - aligned sequence FASTA file (X.fas)
- **Output:** Output mutation tree file (X.treefile) - Newick format

## Individual Command 
`iqtree2 -T <THREADS> -s <ALIGNMENT.ALN> -m GTR+I+G`
- `<THREADS>`: How many threads to use
  - Use auto to use the best number of cores aka threads based on user's computer
- `<ALIGNMENT.ALN>`: The input alignment (X.aln) file (produced by Step 1)
- `-m`: Choose substitution model for your data
  - Base substitution rates (DNA models): GTR - "General time reversible model with unequal rates and unequal base frequency"
    - http://www.iqtree.org/doc/Substitution-Models#dna-models
  - Rate heterogeneity across sites model: +I+G - "invariable site plus discrete Gamma model"
    - http://www.iqtree.org/doc/Substitution-Models#dna-models
  - Use `-m TEST option` or new ModelFinder `-m MFP` to automatically determines best-fit model for data if unsure which model to use
- `iqtree2` in the command should be replaced with the actual path to executable
  - When you download iqtree2, put in a handy directory such as the current working directory for the project
  - ie. /Users/User_Name/Desktop/Directory_Name/iqtree2 
- This command will produce several files in the current directory/folder. We care about the mutation tree file (X.treefile). This will be one of the inputs to Step 3.
 
# Step 3: Perform Tree Dating using TreeTime
- **Input:** Input mutation tree file (output of Step 2 - X.treefile), input alignment file (output of Step 1 - X.aln), input spreadsheet containing collection dates of each sequence (X.csv)
- **Output:** Output treetime file (Treetime.nexus)

## Individual Command
`treetime --tree <MUTATION_TREE.NWK> --aln <ALIGNMENT.ALN> --dates <DATES.CSV> --outdir <OUTPUT>`
- `<MUTATION_TREE.NWK>`:  Mutation tree file (output of Step 2 - X.treefile)
- `<ALIGNMENT.ALN>`: Alignment file (output of Step 1 - X.aln)
- `<DATES.CSV>`: Spreadsheet containing collection dates of each sequence (X.csv)
  - Two columns in spreadsheet: Strain/sequence names and Date of collection for each sequence
  - The strain/sequence names **must** match the strain/sequence names in the initial input sequence file in Step 1 `<SEQUENCES.FAS>` otherwise treetime cannot build the tree
-  `<OUTPUT>`: Name of desired output directory
- The command will produce a folder/directory matching the name the user chose in `<OUTPUT>`. Inside the folder there will be many files. We care about the Treefile file (Treetime.nexus)

