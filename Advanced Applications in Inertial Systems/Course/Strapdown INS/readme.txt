/************************************************* ************************/
/* Strapdown inertial navigation simulation program */
/************************************************* ************************/

Navigation coordinate system: East-North-Day
Carrier coordinate system: right-front-up
Attitude angle range: -PI/2<=pitch<=PI/2, -PI<roll<=PI, -PI<yaw<=PI
Note that the north-north direction of the program is positive.
For details, refer to the author's thesis: "Study on Strapdown Inertial Navigation Algorithm and Vehicle Integrated Navigation System"
Or "Research on Vehicle Autonomous Positioning System"
Or "Study on the initial alignment of the SINS and its related problems"
Or the "Inertial Instrument Test and Data Analysis" handout

Common (custom) variable symbol description:
Ts: sampling step (cycle)
Att: attitude angle vector [pitch angle; roll angle; azimuth]; qnb: attitude quaternion: Cnb: attitude matrix
Vn: speed [eastward; northward; heaven]
Pos: geographical location [latitude; longitude; height]
Eb: gyro constant drift; web: gyro angle random walk coefficient
Db: add constant zero offset; wdb: add speed random walk coefficient
Wbib: gyro sampling angular velocity; fbsf: adding sampling specific force
Wm: gyro sampling angle increment; vm: additional sampling speed increment
Wnie: Earth rotation in the navigation system projection; wnen: speed causes the navigation system to rotate; gn: gravity vector
Phi: attitude error angle; dvn: speed error; dpos: position error

Global variables:
Glvs global variable initial value

Subroutines include:
A2caw Euler angle differential equation coefficient matrix, a2cwa is its inverse matrix
Askew asks against the array by vector
A2cnb attitude angle is transformed into attitude matrix
A2qnb attitude angle is transformed into attitude quaternion
Rv2q rotation vector is transformed into transform quaternion
Qconj quaternion seeking conjugate
Qmul two quaternion multiplication
Qmulv vector coordinate transformation by transforming quaternions
Q2cnb attitude quaternion is transformed into attitude matrix
Q2att attitude quaternion is converted into attitude angle
Qaddphi real pose quaternion plus platform error angle to calculate quaternion (phi is generally a small misalignment angle)
Qdelphi is calculated by subtracting the platform error angle from the quaternion to obtain the true pose quaternion
Qq2phi extracts the platform error angle from the real pose quaternion and the calculated quaternion
Qaddafa real attitude quaternion plus Euler platform error angle to calculate quaternion (afa can be a large misalignment angle)
Qdelafa is calculated by calculating the quaternion minus the Euler platform error angle to obtain the true pose quaternion
Qq2afa extracts the Euler platform error angle from the real pose quaternion and the calculated quaternion
Convert q2rv transform quaternion to rotation vector
M2att attitude matrix is ​​transformed into attitude angle
M2qnb attitude matrix is ​​transformed into attitude quaternion
Dv2atti dual vector pose (for example, static coarse alignment matrix)
Align_i0 Anti-sloshing coarse alignment based on inertial reference frame
Compasskxyz calculates compass alignment control rate parameters
compassCmd compass alignment command angular rate control rate
Earth calculates functions related to earth parameters
Cnscl cone and rowing error compensation
Trj simple trajectory generation algorithm
Drift_bias set gyro and accelerometer error parameters
ImuAddErr adds simulation error to Imu sensor increments
ImuStatic obtains static Imu sensor measurement information
Nav_init navigation parameter initialization
Nav_err calculates navigation error
Sins strapdown inertial navigation algorithm
DR dead reckoning algorithm
Getf seeking Kalman filter system matrix Ft
Kalman discrete Kalman filter
Discretization of kfdis continuous Kalman filter equation of state
RLS recursive least squares
Ekf extended Kalman filter
Large_phi_model large attitude error angle nonlinear state model
Ukf UKF filtering (measurement linearity, noise additive)
Markov simulation Markov process
Timedisp program running time progress display
Make_dll compiles m files into dll files for faster operation

Subroutines under tools include
Allan arun analysis of variance
Mean n averaging n consecutive numbers
Incre calculates the cumulative increment
R2d, r2dm, r2dms convert from radians to degrees/degrees/degrees and minutes
D2r, dm2r, dms2r from degrees / degrees / degrees to seconds to radians
Rfu converts the inertial device output (if not "right-front-up") to the "right-front-up" direction

Steps for usage:
(1) Set the path, select in Matlab main interface: File -> SetPath... -> Add with
Subfolders... -> Select the directory where this file is located -> OK -> Save -> Close
(2) Run test_trj to generate tracks
(3) Run test_sins for simulation of strapdown inertial navigation
(4) Run test_SINS_err_verify to verify the accuracy of the inference error equation derivation
(5) Run test_SINS_GPS for SINS/GPS combined simulation
(6) Run test_SINS_DR to perform SINS/DR combination simulation.

Other demo programs include:
Test_cone_error cone error simulation
Test_scull_error rowing error simulation
Test_drift_random_walk random walk simulation
Test_align_kalman_fn kalman filter initial alignment simulation
Test_align_compass_static static pedestal compass initial alignment simulation
Test_align_compass_moving dynamic pedestal compass initial alignment simulation
Test_align_compass_reverse initial alignment simulation of compass method based on inverse algorithm
Test_align_ekf_5, test_align_ekf_10 Large Azimuth Error Angle EKF Filter Initial Alignment Simulation
Test_align_ukf large attitude error angle UKF filter initial alignment simulation
Test_sins_static static strapdown inertial navigation solution simulation
Test_sins_static_long_time long time inertial error law
test_SINS_GPS_static Static SINS/GPS combined Kalman filter demo
Test_sins_ccd static inertial/star sensor combination (idealized)
    Transfer alignment:
Test_transfer_trj generates a winged maneuver
Test_transfer_imu_sim produces a transfer alignment sub-inertial output
Test_transfer_align speed + attitude matching transfer alignment simulation

Brief description of the measured data:
Lgimu.dat Inertial level laser inertia group placed on the car, there are people getting on and off the vehicle interference, collecting about half an hour, sampling period 8ms;
A total of 6 columns of data: the first 3 is the angular increment (equivalent 0.932 angular seconds / pulse), the last 3 is the speed increment (equivalent 500ug * s / pulse,
1ug*s=9.7803267714*1e-6m/s); collection location (Inertial Technology Laboratory, Xigong University of Automation) Location:
The latitude is 34.246048 degrees; the longitude is 108.909663 degrees; the height is 380 meters. The author only made the compass alignment analysis, the reader
Other alignment methods, such as inertial navigation, can also be performed on the data.
Test_align_compass_lgimu measured laser SIMU data compass alignment
Test_align_para_reco_lgimu parameter identification initial fine alignment
Test_align_kalman_vn_lgimu Kalman initial alignment with velocity as the observation
