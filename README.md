<p>The name of a molecule is a very important information. But the output file of meeko did not include the name line.</p>
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
<p>After the modification, it looks likes as following:</p>
<pre line="301" lang="python">
        else:
            preparator.prepare(mol)
            pdbqt_string = preparator.write_pdbqt_string()
            title = mol.GetProp('_Name')
            pdbqt_string = 'REMARK  Name = '+title+'\n'+pdbqt_string
            mol_name = preparator.setup.name # setup.name may be None
            output(pdbqt_string, mol_name)
            if args.verbose: preparator.show_setup()
</pre>
