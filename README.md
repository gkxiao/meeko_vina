<h2>The issue of missing molecule names in Meeko PDBQT (V0.4.0)</h2>
<p>The name of a molecule is a very important information. However, there is an issue of missing molecule names in Meeko PDBQT (V0.4.0). Here is an example to show the issue.</p>
<p>test.sdf:  an example of SDF file with molecular name "test"</p>
<pre line="1" lang="python">
test
 OpenBabel06272315433D

  4  3  0  0  0  0  0  0  0  0999 V2000
    1.0507    0.0872   -0.0485 C   0  0  0  0  0  0  0  0  0  0  0  0
    2.2754    0.0872   -0.0485 O   0  0  0  0  0  0  0  0  0  0  0  0
    0.4632    0.7434   -0.7103 H   0  0  0  0  0  0  0  0  0  0  0  0
    0.4632   -0.5690    0.6133 H   0  0  0  0  0  0  0  0  0  0  0  0
  1  2  2  0  0  0  0
  1  3  1  0  0  0  0
  1  4  1  0  0  0  0
M  END
$$$$
</pre>
<p>test.pdbqt: the output PDBQT file prepared with meeko command:</p>
<pre line="1" lang="python">
mk_prepare_ligand.py -i test.sdf -o test.pdbqt
</pre>
<p>Here is the output:</p>
<pre line="1" lang="python">
REMARK SMILES C=O
REMARK SMILES IDX 1 1 2 2
REMARK H PARENT
REMARK Flexibility Score: inf
ROOT
ATOM      1  C   UNL     1       1.051   0.087  -0.049  1.00  0.00     0.307 C
ATOM      2  O   UNL     1       2.275   0.087  -0.049  1.00  0.00    -0.307 OA
ENDROOT
TORSDOF 0
</pre>
<p>There is no name line in the REMARK lines. Here is an example with Name line:</p>
<pre line="1" lang="python">
REMARK  Name = test
REMARK  0 active torsions:
REMARK  status: ('A' for Active; 'I' for Inactive)
REMARK                            x       y       z     vdW  Elec       q    Type
REMARK                         _______ _______ _______ _____ _____    ______ ____
ROOT
ATOM      1  C   UNL     1       1.051   0.087  -0.049  0.00  0.00    +0.000 C
ATOM      2  O   UNL     1       2.275   0.087  -0.049  0.00  0.00    +0.000 OA
ENDROOT
TORSDOF 0
</pre>
<h2>How to fix it?</h2>
<p>After the line 303 in the mk_prepare_ligand.py, insert two lines:</p>
<pre line="1" lang="python">
title = mol.GetProp('_Name')
pdbqt_string = 'REMARK  Name = '+title+'\n'+pdbqt_string
</pre>
<p>Save the revised file as mk_prepare_ligand_new.py.</p>
<p>Try it：</p>
<pre line="1" lang="python">
mk_prepare_ligand_new.py -i actives_final.sdf \
  --multimol_outdir actives \
   --multimol_prefix actives
</pre>
<p>Check one pdbqt file：</p>
<pre line="1" lang="python">
(rdkit2020) gkxiao@master:/public/gkxiao/work/meeko$ head actives/actives-1.pdbqt
REMARK  Name = CHEMBL430421
REMARK SMILES COC(=O)c1c(-c2cc(OC)c(OC)c(OC)c2)c2cc(OC)c(OC)cc2c(=O)n1C1CCCC1
REMARK SMILES IDX 6 1 19 2 20 3 21 4 24 5 27 6 28 7 29 8 30 9 31 10 5 11 7 12
REMARK SMILES IDX 9 13 12 14 8 15 15 16 18 17 10 18 11 19 13 20 14 21 16 22
REMARK SMILES IDX 17 23 3 24 4 25 2 26 1 27 32 28 36 29 33 30 35 31 34 32
REMARK SMILES IDX 22 33 23 34 25 35 26 36
REMARK H PARENT
REMARK Flexibility Score: inf
ROOT
ATOM      1  C   UNL     1      -4.776   0.390  -0.036  1.00  0.00     0.021 A
</pre>
