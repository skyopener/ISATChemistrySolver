# ISATChemistrySolver
Use S.B. Pope's ISAT-CK7 code to speed up chemistry integration in OpenFOAM

Before proceeding, get the following software:

 ISAT-CK7:          https://tcg.mae.cornell.edu/ISATCK7/
 ChemkinII 4.5      Citing Pope: I cannot assist the user in obtaining Chemkin II. 

Optional:
 For more functionality of ISAT-CK7, you can install ADIFOR


Put the ISAT interface (my code) for OpenFOAM in ~/OpenFOAM/user/lib
cd ~/OpenFOAM/user/lib/ISAT
wmake

Enter ChemkinII source code directory: 

make
cp *.a ../ISAT_dependencies/lib
cd ../ISAT
make
cp *.so $FOAM_USER_LIBBIN

For a case with reactingFoam use this in 

chemistryType
{
    chemistrySolver   ISAT;
    chemistryThermo   psi;
}

You need to produce the file chem.bin and put it in the case directory
In your case dir:
cd chemkin
CHEMKIN_COMPILE_DIR/ckinterp
mv chem.bin ..

ckinterp assumes there are two files present: chem.inp and therm.dat to produce chem.bin

Please make sure your solver also uses the chemistryReader chemkinReader:

thermoPhysicalProperties:
    CHEMKINFile     "$FOAM_CASE/chemkin/chem.inp";
    CHEMKINThermoFile "$FOAM_CASE/chemkin/therm.dat";

Such that OpenFOAM and ISAT use the same species, and also use them in the same
order. Further, this guarantees that the same thermodynamical data is used by both
OpenFOAM and ISAT/Chemkin

Further you need a ISAT file called "streams.in". See ISAT-CK7 code for info.
The OpenFOAM ISAT interface does not use those streams, but the file is needed.

If you fix time step and have a constant pressure (ie low Mach solver) you can
have con_dt=1 and con_pr=1 in file called "ci.nml" in case folder: (make sure
your solvers thermodynamic pressure matches that of the stream in "stream.in"

&cinml
con_pr=1
con_dt=1
/


In controlDict you need:

libs
(
    "libISATChemistrySolver.so"
    "liblapack.so.3"
    "libadifor.so"
    "libisat7_ser.so"
    "libceq.so"
    "libck_ext.so"
    "libell.so"
    "libice_pic.so"
    "libisatab_ser.so"
    "libisatck_ext.so"
    "libisatck.so"
    "libisat_rnu.so"
    "libsell.so"
);

