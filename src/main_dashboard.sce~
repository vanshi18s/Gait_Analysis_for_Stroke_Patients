f=figure('figure_position',[-8,-8],'figure_size',[1552,832],'auto_resize','on','background',[35],'figure_name','Advanced XAI Gait Analysis','color_map',[0,0,0;0,0,1;0,1,0;0,1,1;1,0,0;1,0,1;1,1,0;1,1,1;0,0,0.5647059;0,0,0.6901961;0,0,0.8156863;0.5294118,0.8078431,1;0,0.5647059,0;0,0.6901961,0;0,0.8156863,0;0,0.5647059,0.5647059;0,0.6901961,0.6901961;0,0.8156863,0.8156863;0.5647059,0,0;0.6901961,0,0;0.8156863,0,0;0.5647059,0,0.5647059;0.6901961,0,0.6901961;0.8156863,0,0.8156863;0.5019608,0.1882353,0;0.627451,0.2509804,0;0.7529412,0.3764706,0;1,0.5019608,0.5019608;1,0.627451,0.627451;1,0.7529412,0.7529412;1,0.8784314,0.8784314;1,0.8431373,0;0.8,0.8,0.8;0.9333333,0.9333333,0.9333333;0.85,0.9,0.95;0,0.2,0.4],'dockable','off','infobar_visible','off','toolbar_visible','off','menubar_visible','off','default_axes','on','visible','off');

// --- Global Variables ---
global kin_data shap_data active_model base_threshold ml_colors;
global hc_pelvis_bone hc_thigh hc_calf hc_foot hc_ank_heel hc_pel hc_hip hc_kne hc_ank;
global sp_pelvis_bone sp_thigh sp_calf sp_foot sp_ank_heel sp_pel sp_hip sp_kne sp_ank;
global p_ank_sp p_kne_sp p_hip_sp p_pel_sp;
global p_ank_hc p_kne_hc p_hip_hc p_pel_hc;
global model_names model_accuracies;
global audio_y audio_Fs audio_playing; 

active_model = 0; base_threshold = 0.76; shap_data = []; audio_playing = 0;
ml_colors = ones(200, 4) * 2; 

model_names = ['Coarse Tree', 'Logistic Regression', 'Naive Bayes', 'Cubic SVM', 'Fine KNN', 'Bagged Trees', 'Medium NN'];
model_accuracies = ['88.2%', '92.3%', '91.5%', '92.3%', '90.0%', '91.6%', '92.3%'];

// --- UI Elements ---
handles.Title = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[24],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','center','Position',[0.15,0.92,0.7,0.06],'String','Kinematics GaitSim: XAI Stroke vs Healthy Gait Simulator','Style','text');
handles.LiveScanner = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','center','Position',[0.35,0.87,0.3,0.04],'String','SYSTEM IDLE: Select ML Model','Style','text');
handles.Sensitivity = uicontrol(f,'unit','normalized','BackgroundColor',[1,1,1],'Max',[3.0],'Min',[0.1],'Position',[0.35,0.13,0.3,0.03],'SliderStep',[0.1,0.5],'Style','slider','Value',[1.0],'Callback','Sens_callback(handles)');
handles.SensLabel = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','center','Position',[0.35,0.09,0.3,0.03],'String','Anomaly Sensitivity Multiplier: 1.0x','Style','text');
handles.LabelHC = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[14],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','center','Position',[0.15,0.82,0.19,0.04],'String','Healthy Person','Style','text');
handles.LabelSP = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[14],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','center','Position',[0.40,0.82,0.19,0.04],'String','Stroke Patient','Style','text');
handles.AudioBtn = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[10],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0.94,0.93,0.05,0.04],'String','AUDIO','Style','pushbutton','Callback','AudioBtn_callback(handles)');

// Graph Legend
handles.LegendLines = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[11],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','right','Position',[0.64,0.085,0.18,0.03],'String','Lines: Blue=HC, Black=SP   |   ','Style','text');
handles.BlueDot = uicontrol(f,'unit','normalized','BackgroundColor',[0,0,1],'Position',[0.83,0.09,0.008,0.02],'Style','text');
handles.BlueText = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[11],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','left','Position',[0.84,0.085,0.06,0.03],'String','= Healthy','Style','text');
handles.RedDot = uicontrol(f,'unit','normalized','BackgroundColor',[1,0,0],'Position',[0.90,0.09,0.008,0.02],'Style','text');
handles.RedText = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontName','Tahoma','FontSize',[11],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'HorizontalAlignment','left','Position',[0.91,0.085,0.06,0.03],'String','= Anomaly','Style','text');

// --- ML Diagnostics Dashboard ---
handles.ResFrame = uicontrol(f, 'unit', 'normalized', 'Position', [0.005, 0.11, 0.135, 0.39], 'Style', 'frame', 'BackgroundColor', [0.90, 0.94, 0.98]);
handles.ResTitle = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.46, 0.125, 0.03], 'Style', 'text', 'String', 'ML DIAGNOSTICS', 'FontWeight', 'bold', 'FontSize', 12, 'BackgroundColor', [0.90, 0.94, 0.98], 'ForegroundColor', [0, 0.2, 0.4], 'HorizontalAlignment', 'center');
handles.ResAnkle = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.41, 0.125, 0.03], 'Style', 'text', 'String', 'Ankle: -', 'FontSize', 11, 'BackgroundColor', [0.90, 0.94, 0.98], 'HorizontalAlignment', 'left');
handles.ResKnee = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.37, 0.125, 0.03], 'Style', 'text', 'String', 'Knee: -', 'FontSize', 11, 'BackgroundColor', [0.90, 0.94, 0.98], 'HorizontalAlignment', 'left');
handles.ResHip = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.33, 0.125, 0.03], 'Style', 'text', 'String', 'Hip: -', 'FontSize', 11, 'BackgroundColor', [0.90, 0.94, 0.98], 'HorizontalAlignment', 'left');
handles.ResPelvis = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.29, 0.125, 0.03], 'Style', 'text', 'String', 'Pelvis: -', 'FontSize', 11, 'BackgroundColor', [0.90, 0.94, 0.98], 'HorizontalAlignment', 'left');
handles.ResAffected = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.23, 0.125, 0.04], 'Style', 'text', 'String', 'XAI Driver: NONE', 'FontWeight', 'bold', 'FontSize', 11, 'BackgroundColor', [0.90, 0.94, 0.98], 'ForegroundColor', [0, 0, 0], 'HorizontalAlignment', 'center');
handles.ResDiagnosis = uicontrol(f, 'unit', 'normalized', 'Position', [0.01, 0.12, 0.125, 0.10], 'Style', 'text', 'String', 'WAITING FOR MODEL', 'FontWeight', 'bold', 'FontSize', 12, 'BackgroundColor', [0.90, 0.94, 0.98], 'ForegroundColor', [0.5, 0.5, 0.5], 'HorizontalAlignment', 'center');

// --- ML Radio Buttons & Controls ---
handles.CT=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.88,0.13,0.04],'String','Coarse Tree','Style','radiobutton','Callback','CT_callback(handles)')
handles.LR=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.82,0.13,0.04],'String','Logistic Regression','Style','radiobutton','Callback','LR_callback(handles)')
handles.NB=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.76,0.13,0.04],'String','Naive Bayes','Style','radiobutton','Callback','NB_callback(handles)')
handles.CSVM=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.70,0.13,0.04],'String','Cubic SVM','Style','radiobutton','Callback','CSVM_callback(handles)')
handles.FKNN=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.64,0.13,0.04],'String','Fine KNN','Style','radiobutton','Callback','FKNN_callback(handles)')
handles.BT=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.58,0.13,0.04],'String','Bagged Trees','Style','radiobutton','Callback','BT_callback(handles)')
handles.MNN=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0,0.52,0.13,0.04],'String','Medium NN','Style','radiobutton','Callback','MNN_callback(handles)')
handles.TimeSeries=uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'Position',[0,0.05,0.899,0.04],'String','Gait Cycle','Style','slider','Callback','TimeSeries_callback(handles)')
handles.StartStop = uicontrol(f,'unit','normalized','BackgroundColor',[0.85,0.9,0.95],'FontSize',[12],'FontWeight','bold','ForegroundColor',[0,0.2,0.4],'Position',[0.898,0.05,0.10,0.04],'String','PLAY','Style','checkbox','Callback','StartStop_callback(handles)')

// --- Axes Setup --- 
handles.HC= newaxes(); handles.HC.margins = [0.02 0.02 0.02 0.02]; handles.HC.axes_bounds = [0.15,0.18,0.19,0.62]; handles.HC.background = 8; handles.HC.box = 'on'; handles.HC.thickness = 2; handles.HC.foreground = 36;
handles.SP= newaxes(); handles.SP.margins = [0.02 0.02 0.02 0.02]; handles.SP.axes_bounds = [0.40,0.18,0.19,0.62]; handles.SP.background = 8; handles.SP.box = 'on'; handles.SP.thickness = 2; handles.SP.foreground = 36;
handles.Ankle= newaxes(); handles.Ankle.margins = [0.08 0.08 0.15 0.15]; handles.Ankle.axes_bounds = [0.65, 0.11, 0.14, 0.32]; handles.Ankle.background = 8; handles.Ankle.box = 'on'; handles.Ankle.thickness = 2; handles.Ankle.foreground = 36;
handles.Knee= newaxes(); handles.Knee.margins = [0.08 0.08 0.15 0.15]; handles.Knee.axes_bounds = [0.82, 0.11, 0.14, 0.32]; handles.Knee.background = 8; handles.Knee.box = 'on'; handles.Knee.thickness = 2; handles.Knee.foreground = 36;
handles.Hip= newaxes(); handles.Hip.margins = [0.08 0.08 0.15 0.15]; handles.Hip.axes_bounds = [0.65, 0.47, 0.14, 0.32]; handles.Hip.background = 8; handles.Hip.box = 'on'; handles.Hip.thickness = 2; handles.Hip.foreground = 36;
handles.Pelvis= newaxes(); handles.Pelvis.margins = [0.08 0.08 0.15 0.15]; handles.Pelvis.axes_bounds = [0.82, 0.47, 0.14, 0.32]; handles.Pelvis.background = 8; handles.Pelvis.box = 'on'; handles.Pelvis.thickness = 2; handles.Pelvis.foreground = 36;

// --- GUI Close Event ---
function close_gui()
    playsnd(0, 8000); 
    delete(gcf());
endfunction
f.closerequestfcn = 'close_gui';

disp('Loading Kinematics Data...');
try
    kin_data = csvRead('mean_trajectories_for_scilab.csv');
catch
    disp('ERROR: mean_trajectories_for_scilab.csv not found.');
end

// --- 3D Forward Kinematics ---
function [X, Y, Z]=get_3D_leg(pelvis_ang, hip_ang, knee_ang, ankle_ang, t_pct)
    L_pelvis = 12; L_thigh = 42; L_calf = 40; L_foot = 15; L_heel = 5;
    x0 = 40 - (t_pct / 100) * 80; y0 = 0; z0 = 110 - (3.5 * abs(sind(t_pct * 3.6 * 2))); 
    x1 = x0 - L_pelvis * sind(pelvis_ang); y1 = 0; z1 = z0 - L_pelvis * cosd(pelvis_ang);
    x2 = x1 - L_thigh * sind(hip_ang); y2 = 0; z2 = z1 - L_thigh * cosd(hip_ang);
    x3 = x2 - L_calf * sind(hip_ang - knee_ang); y3 = 0; z3 = z2 - L_calf * cosd(hip_ang - knee_ang);
    foot_ang = hip_ang - knee_ang - ankle_ang + 90;
    x4 = x3 - L_foot * sind(foot_ang); y4 = 0; z4 = z3 - L_foot * cosd(foot_ang); 
    x5 = x3 + L_heel * sind(foot_ang); y5 = 0; z5 = z3 + L_heel * cosd(foot_ang); 
    X = [x0, x1, x2, x3, x4, x5]; Y = [y0, y1, y2, y3, y4, y5]; Z = [z0, z1, z2, z3, z4, z5];
endfunction

// --- Graphics Initialization ---
drawlater();

sca(handles.HC); [X_hc, Y_hc, Z_hc] = get_3D_leg(kin_data(1,14), kin_data(1,10), kin_data(1,6), kin_data(1,2), 0); 
param3d([X_hc(1), X_hc(2)], [Y_hc(1), Y_hc(2)], [Z_hc(1), Z_hc(2)]); hc_pelvis_bone = gce(); hc_pelvis_bone.thickness = 4; hc_pelvis_bone.foreground = 33; 
param3d([X_hc(2), X_hc(3)], [Y_hc(2), Y_hc(3)], [Z_hc(2), Z_hc(3)]); hc_thigh = gce(); hc_thigh.thickness = 7; hc_thigh.foreground = 33; 
param3d([X_hc(3), X_hc(4)], [Y_hc(3), Y_hc(4)], [Z_hc(3), Z_hc(4)]); hc_calf = gce(); hc_calf.thickness = 5; hc_calf.foreground = 33; 
param3d([X_hc(6), X_hc(5)], [Y_hc(6), Y_hc(5)], [Z_hc(6), Z_hc(5)]); hc_foot = gce(); hc_foot.thickness = 4; hc_foot.foreground = 33; 
param3d([X_hc(4), X_hc(6)], [Y_hc(4), Y_hc(6)], [Z_hc(4), Z_hc(6)]); hc_ank_heel = gce(); hc_ank_heel.thickness = 4; hc_ank_heel.foreground = 33; 

param3d(X_hc(1), Y_hc(1), Z_hc(1)); hc_pel = gce(); hc_pel.mark_mode='on'; hc_pel.mark_style=9; hc_pel.mark_size=5; hc_pel.mark_background=2; hc_pel.mark_foreground=2;
param3d(X_hc(2), Y_hc(2), Z_hc(2)); hc_hip = gce(); hc_hip.mark_mode='on'; hc_hip.mark_style=9; hc_hip.mark_size=6; hc_hip.mark_background=2; hc_hip.mark_foreground=2;
param3d(X_hc(3), Y_hc(3), Z_hc(3)); hc_kne = gce(); hc_kne.mark_mode='on'; hc_kne.mark_style=9; hc_kne.mark_size=6; hc_kne.mark_background=2; hc_kne.mark_foreground=2;
param3d(X_hc(4), Y_hc(4), Z_hc(4)); hc_ank = gce(); hc_ank.mark_mode='on'; hc_ank.mark_style=9; hc_ank.mark_size=6; hc_ank.mark_background=2; hc_ank.mark_foreground=2;
a = gca(); a.isoview = 'on'; a.data_bounds = [-50, -10, 0; 50, 10, 110]; a.rotation_angles = [80, 45]; 

sca(handles.SP); [X_sp, Y_sp, Z_sp] = get_3D_leg(kin_data(1,16), kin_data(1,12), kin_data(1,8), kin_data(1,4), 0); 
param3d([X_sp(1), X_sp(2)], [Y_sp(1), Y_sp(2)], [Z_sp(1), Z_sp(2)]); sp_pelvis_bone = gce(); sp_pelvis_bone.thickness = 4; sp_pelvis_bone.foreground = 33; 
param3d([X_sp(2), X_sp(3)], [Y_sp(2), Y_sp(3)], [Z_sp(2), Z_sp(3)]); sp_thigh = gce(); sp_thigh.thickness = 7; sp_thigh.foreground = 33; 
param3d([X_sp(3), X_sp(4)], [Y_sp(3), Y_sp(4)], [Z_sp(3), Z_sp(4)]); sp_calf = gce(); sp_calf.thickness = 5; sp_calf.foreground = 33; 
param3d([X_sp(6), X_sp(5)], [Y_sp(6), Y_sp(5)], [Z_sp(6), Z_sp(5)]); sp_foot = gce(); sp_foot.thickness = 4; sp_foot.foreground = 33; 
param3d([X_sp(4), X_sp(6)], [Y_sp(4), Y_sp(6)], [Z_sp(4), Z_sp(6)]); sp_ank_heel = gce(); sp_ank_heel.thickness = 4; sp_ank_heel.foreground = 33; 

param3d(X_sp(1), Y_sp(1), Z_sp(1)); sp_pel = gce(); sp_pel.mark_mode='on'; sp_pel.mark_style=9; sp_pel.mark_size=6; sp_pel.mark_background=2; sp_pel.mark_foreground=2;
param3d(X_sp(2), Y_sp(2), Z_sp(2)); sp_hip = gce(); sp_hip.mark_mode='on'; sp_hip.mark_style=9; sp_hip.mark_size=7; sp_hip.mark_background=2; sp_hip.mark_foreground=2;
param3d(X_sp(3), Y_sp(3), Z_sp(3)); sp_kne = gce(); sp_kne.mark_mode='on'; sp_kne.mark_style=9; sp_kne.mark_size=7; sp_kne.mark_background=2; sp_kne.mark_foreground=2;
param3d(X_sp(4), Y_sp(4), Z_sp(4)); sp_ank = gce(); sp_ank.mark_mode='on'; sp_ank.mark_style=9; sp_ank.mark_size=7; sp_ank.mark_background=2; sp_ank.mark_foreground=2;
a2 = gca(); a2.isoview = 'on'; a2.data_bounds = [-50, -10, 0; 50, 10, 110]; a2.rotation_angles = [80, 45]; 

// --- 2D Graph Initialization ---
time_pct = kin_data(:,1);

sca(handles.Ankle); plot(time_pct, kin_data(:,2), 'b-', time_pct, kin_data(:,4), 'k--'); 
a=gca(); a.title.text = 'Ankle Angle'; a.title.font_foreground = 36; a.title.font_style = 6; a.title.font_size = 4;
plot(time_pct(1), kin_data(1,2)); p_ank_hc = gce(); p_ank_hc = p_ank_hc.children(1); p_ank_hc.mark_mode='on'; p_ank_hc.mark_style=9; p_ank_hc.mark_size=6; p_ank_hc.mark_background=2; p_ank_hc.mark_foreground=2;
plot(time_pct(1), kin_data(1,4)); p_ank_sp = gce(); p_ank_sp = p_ank_sp.children(1); p_ank_sp.mark_mode='on'; p_ank_sp.mark_style=9; p_ank_sp.mark_size=8; p_ank_sp.mark_background=2; p_ank_sp.mark_foreground=2;

sca(handles.Knee); plot(time_pct, kin_data(:,6), 'b-', time_pct, kin_data(:,8), 'k--'); 
a=gca(); a.title.text = 'Knee Angle'; a.title.font_foreground = 36; a.title.font_style = 6; a.title.font_size = 4;
plot(time_pct(1), kin_data(1,6)); p_kne_hc = gce(); p_kne_hc = p_kne_hc.children(1); p_kne_hc.mark_mode='on'; p_kne_hc.mark_style=9; p_kne_hc.mark_size=6; p_kne_hc.mark_background=2; p_kne_hc.mark_foreground=2;
plot(time_pct(1), kin_data(1,8)); p_kne_sp = gce(); p_kne_sp = p_kne_sp.children(1); p_kne_sp.mark_mode='on'; p_kne_sp.mark_style=9; p_kne_sp.mark_size=8; p_kne_sp.mark_background=2; p_kne_sp.mark_foreground=2;

sca(handles.Hip); plot(time_pct, kin_data(:,10), 'b-', time_pct, kin_data(:,12), 'k--'); 
a=gca(); a.title.text = 'Hip Angle'; a.title.font_foreground = 36; a.title.font_style = 6; a.title.font_size = 4;
plot(time_pct(1), kin_data(1,10)); p_hip_hc = gce(); p_hip_hc = p_hip_hc.children(1); p_hip_hc.mark_mode='on'; p_hip_hc.mark_style=9; p_hip_hc.mark_size=6; p_hip_hc.mark_background=2; p_hip_hc.mark_foreground=2;
plot(time_pct(1), kin_data(1,12)); p_hip_sp = gce(); p_hip_sp = p_hip_sp.children(1); p_hip_sp.mark_mode='on'; p_hip_sp.mark_style=9; p_hip_sp.mark_size=8; p_hip_sp.mark_background=2; p_hip_sp.mark_foreground=2;

sca(handles.Pelvis); plot(time_pct, kin_data(:,14), 'b-', time_pct, kin_data(:,16), 'k--'); 
a=gca(); a.title.text = 'Pelvis Angle'; a.title.font_foreground = 36; a.title.font_style = 6; a.title.font_size = 4;
plot(time_pct(1), kin_data(1,14)); p_pel_hc = gce(); p_pel_hc = p_pel_hc.children(1); p_pel_hc.mark_mode='on'; p_pel_hc.mark_style=9; p_pel_hc.mark_size=6; p_pel_hc.mark_background=2; p_pel_hc.mark_foreground=2;
plot(time_pct(1), kin_data(1,16)); p_pel_sp = gce(); p_pel_sp = p_pel_sp.children(1); p_pel_sp.mark_mode='on'; p_pel_sp.mark_style=9; p_pel_sp.mark_size=8; p_pel_sp.mark_background=2; p_pel_sp.mark_foreground=2;

drawnow();
f.visible = 'on';

// --- ML Anomaly Calculation Engine ---
function calculate_ml_anomalies(handles)
    global kin_data shap_data active_model base_threshold ml_colors;
    global model_names model_accuracies;
    
    if active_model == 0 then return; end
    
    user_mult = get(handles.Sensitivity, 'Value');
    total_rows = size(kin_data, 1);
    ml_colors = ones(total_rows, 4) * 2; 
    
    ank_red = 0; kne_red = 0; hip_red = 0; pel_red = 0;
    ank_blue = 0; kne_blue = 0; hip_blue = 0; pel_blue = 0;
    
    // XAI Logic
    xai_primary = 'NONE';
    if ~isempty(shap_data) then
        if size(shap_data, 2) >= 10 then
            w_ank = mean(abs(shap_data(:,9))); 
            w_kne = 0; 
            w_hip = mean(abs(shap_data(:,2))) + mean(abs(shap_data(:,5))) + mean(abs(shap_data(:,8))) + mean(abs(shap_data(:,10)));
            w_pel = mean(abs(shap_data(:,1))) + mean(abs(shap_data(:,3))) + mean(abs(shap_data(:,4))) + mean(abs(shap_data(:,6))) + mean(abs(shap_data(:,7)));
        else
            w_ank = mean(abs(shap_data(:,1))); 
            w_kne = mean(abs(shap_data(:,2)));
            w_hip = mean(abs(shap_data(:,3))); 
            w_pel = mean(abs(shap_data(:,4)));
        end
        
        shap_weights = [w_ank, w_kne, w_hip, w_pel];
        [max_shap, max_idx] = max(shap_weights);
        joint_names = ['Ankle', 'Knee', 'Hip', 'Pelvis'];
        xai_primary = joint_names(max_idx);
        
        set(handles.ResAffected, 'String', 'XAI Driver: ' + xai_primary);
    else
        set(handles.ResAffected, 'String', 'XAI Driver: N/A');
    end

    // Kinematic Deviation Logic
    allowed_deviation = 5.0 / user_mult; 
    
    for i = 1:total_rows
        dev_ank = abs(kin_data(i,2) - kin_data(i,4));
        dev_kne = abs(kin_data(i,6) - kin_data(i,8));
        dev_hip = abs(kin_data(i,10) - kin_data(i,12));
        dev_pel = abs(kin_data(i,14) - kin_data(i,16));
        
        if dev_ank > allowed_deviation then ml_colors(i,1) = 5; ank_red = ank_red + 1; else ank_blue = ank_blue + 1; end 
        if dev_kne > allowed_deviation then ml_colors(i,2) = 5; kne_red = kne_red + 1; else kne_blue = kne_blue + 1; end
        if dev_hip > allowed_deviation then ml_colors(i,3) = 5; hip_red = hip_red + 1; else hip_blue = hip_blue + 1; end
        if dev_pel > allowed_deviation then ml_colors(i,4) = 5; pel_red = pel_red + 1; else pel_blue = pel_blue + 1; end
    end
    
    set(handles.ResAnkle, 'String',  '  Ankle:  ' + string(ank_red) + ' Red | ' + string(ank_blue) + ' Blue');
    set(handles.ResKnee,  'String',  '  Knee:   ' + string(kne_red) + ' Red | ' + string(kne_blue) + ' Blue');
    set(handles.ResHip,   'String',  '  Hip:    ' + string(hip_red) + ' Red | ' + string(hip_blue) + ' Blue');
    set(handles.ResPelvis,'String',  '  Pelvis: ' + string(pel_red) + ' Red | ' + string(pel_blue) + ' Blue');
    
    arr_reds = [ank_red, kne_red, hip_red, pel_red];
    [max_red_val, max_idx] = max(arr_reds);
    
    acc_str = model_accuracies(active_model);
    
    if max_red_val >= (total_rows * 0.20) then 
        diagnosis = 'STROKE PATTERN' + ascii(10) + 'Conf: ' + acc_str; 
        diag_color = [0.8, 0, 0]; 
    elseif max_red_val > (total_rows * 0.05) then 
        diagnosis = 'MILD DEVIATION' + ascii(10) + 'Conf: ' + acc_str;
        diag_color = [0.8, 0.4, 0]; 
    else 
        diagnosis = 'HEALTHY GAIT' + ascii(10) + 'Conf: ' + acc_str; 
        diag_color = [0, 0.5, 0]; 
    end
    
    set(handles.ResDiagnosis, 'String', diagnosis);
    set(handles.ResDiagnosis, 'ForegroundColor', diag_color);
    
    TimeSeries_callback(handles);
endfunction

// --- Callbacks ---
function AudioBtn_callback(handles)
    global audio_y audio_Fs audio_playing;
    
    if isempty(audio_playing) then audio_playing = 0; end
    
    if audio_playing == 1 then
        playsnd(0, 8000); 
        audio_playing = 0;
        set(handles.AudioBtn, 'String', 'AUDIO');
        set(handles.AudioBtn, 'ForegroundColor', [0, 0.2, 0.4]);
    else
        if isempty(audio_y) then
            try
                [audio_y, audio_Fs] = wavread('instructions.wav');
            catch
                messagebox('Could not load instructions.wav. Ensure the file is inside the exact same folder as your script and your Working Directory is set correctly.', 'Audio Missing', 'error');
                return;
            end
        end
        playsnd(0, 8000); 
        playsnd(audio_y, audio_Fs);
        audio_playing = 1;
        set(handles.AudioBtn, 'String', 'MUTE');
        set(handles.AudioBtn, 'ForegroundColor', [1, 0, 0]);
    end
endfunction

function reset_radios(handles)
    set(handles.CT, 'Value', 0); set(handles.LR, 'Value', 0); set(handles.NB, 'Value', 0); set(handles.CSVM, 'Value', 0); set(handles.FKNN, 'Value', 0); set(handles.BT, 'Value', 0); set(handles.MNN, 'Value', 0);
endfunction

function Sens_callback(handles)
    sens_val = get(handles.Sensitivity, 'Value');
    set(handles.SensLabel, 'String', 'Anomaly Sensitivity Multiplier: ' + string(round(sens_val*10)/10) + 'x');
    calculate_ml_anomalies(handles); 
endfunction

function CT_callback(handles)
    global active_model shap_data base_threshold; active_model = 1; base_threshold = 0.76; 
    reset_radios(handles); set(handles.CT, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Coarse Tree');
    try shap_data = csvRead('Scilab_SHAP_Coarse_Tree.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

function LR_callback(handles)
    global active_model shap_data base_threshold; active_model = 2; base_threshold = 0.76; 
    reset_radios(handles); set(handles.LR, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Logistic Regression');
    try shap_data = csvRead('Scilab_SHAP_Logistic_Regression.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

function NB_callback(handles)
    global active_model shap_data base_threshold; active_model = 3; base_threshold = 0.76; 
    reset_radios(handles); set(handles.NB, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Naive Bayes');
    try shap_data = csvRead('Scilab_SHAP_Kernel_Naive_Bayes.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

function CSVM_callback(handles)
    global active_model shap_data base_threshold; active_model = 4; base_threshold = 0.76; 
    reset_radios(handles); set(handles.CSVM, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Cubic SVM');
    try shap_data = csvRead('Scilab_SHAP_Cubic_SVM.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

function FKNN_callback(handles)
    global active_model shap_data base_threshold; active_model = 5; base_threshold = 0.76;
    reset_radios(handles); set(handles.FKNN, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Fine KNN');
    try shap_data = csvRead('Scilab_SHAP_Fine_KNN.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

function BT_callback(handles)
    global active_model shap_data base_threshold; active_model = 6; base_threshold = 0.76;
    reset_radios(handles); set(handles.BT, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Bagged Trees');
    try shap_data = csvRead('Scilab_SHAP_Bagged_Trees.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

function MNN_callback(handles)
    global active_model shap_data base_threshold; active_model = 7; base_threshold = 0.76;
    reset_radios(handles); set(handles.MNN, 'Value', 1);
    set(handles.LiveScanner, 'String', 'XAI Active: Medium NN');
    try shap_data = csvRead('Scilab_SHAP_Medium_NN.csv'); catch disp('CSV not found.'); shap_data=[]; end
    calculate_ml_anomalies(handles);
endfunction

// --- Animation and Physics Loop ---
function TimeSeries_callback(handles)
    global kin_data active_model ml_colors;
    global hc_pelvis_bone hc_thigh hc_calf hc_foot hc_ank_heel hc_pel hc_hip hc_kne hc_ank;
    global sp_pelvis_bone sp_thigh sp_calf sp_foot sp_ank_heel sp_pel sp_hip sp_kne sp_ank;
    global p_ank_sp p_kne_sp p_hip_sp p_pel_sp;
    global p_ank_hc p_kne_hc p_hip_hc p_pel_hc;
    
    val = get(handles.TimeSeries, 'Value');
    total_rows = size(kin_data, 1);
    row = max(1, floor(val * total_rows));
    if row > total_rows then row = total_rows; end
    t = kin_data(row,1);
    
    drawlater();
    
    // UPDATE BONE POSITIONS
    [X_hc, Y_hc, Z_hc] = get_3D_leg(kin_data(row,14), kin_data(row,10), kin_data(row,6), kin_data(row,2), t); 
    hc_pelvis_bone.data = [X_hc(1:2)', Y_hc(1:2)', Z_hc(1:2)']; hc_thigh.data = [X_hc(2:3)', Y_hc(2:3)', Z_hc(2:3)']; 
    hc_calf.data = [X_hc(3:4)', Y_hc(3:4)', Z_hc(3:4)']; hc_foot.data = [X_hc(6:-1:5)', Y_hc(6:-1:5)', Z_hc(6:-1:5)']; 
    hc_ank_heel.data = [X_hc([4,6])', Y_hc([4,6])', Z_hc([4,6])']; 
    hc_pel.data = [X_hc(1), Y_hc(1), Z_hc(1)]; hc_hip.data = [X_hc(2), Y_hc(2), Z_hc(2)]; 
    hc_kne.data = [X_hc(3), Y_hc(3), Z_hc(3)]; hc_ank.data = [X_hc(4), Y_hc(4), Z_hc(4)];
    
    [X_sp, Y_sp, Z_sp] = get_3D_leg(kin_data(row,16), kin_data(row,12), kin_data(row,8), kin_data(row,4), t); 
    sp_pelvis_bone.data = [X_sp(1:2)', Y_sp(1:2)', Z_sp(1:2)']; sp_thigh.data = [X_sp(2:3)', Y_sp(2:3)', Z_sp(2:3)']; 
    sp_calf.data = [X_sp(3:4)', Y_sp(3:4)', Z_sp(3:4)']; sp_foot.data = [X_sp(6:-1:5)', Y_sp(6:-1:5)', Z_sp(6:-1:5)']; 
    sp_ank_heel.data = [X_sp([4,6])', Y_sp([4,6])', Z_sp([4,6])']; 
    sp_pel.data = [X_sp(1), Y_sp(1), Z_sp(1)]; sp_hip.data = [X_sp(2), Y_sp(2), Z_sp(2)]; 
    sp_kne.data = [X_sp(3), Y_sp(3), Z_sp(3)]; sp_ank.data = [X_sp(4), Y_sp(4), Z_sp(4)];
    
    // UPDATE 2D POSITIONS
    p_ank_hc.data = [t, kin_data(row,2)]; p_ank_sp.data = [t, kin_data(row,4)]; 
    p_kne_hc.data = [t, kin_data(row,6)]; p_kne_sp.data = [t, kin_data(row,8)]; 
    p_hip_hc.data = [t, kin_data(row,10)]; p_hip_sp.data = [t, kin_data(row,12)]; 
    p_pel_hc.data = [t, kin_data(row,14)]; p_pel_sp.data = [t, kin_data(row,16)];
    
    // Apply ML Colors
    if active_model > 0 then
        color_ank = ml_colors(row, 1);
        color_kne = ml_colors(row, 2);
        color_hip = ml_colors(row, 3);
        color_pel = ml_colors(row, 4);
        
        sp_ank.mark_background = color_ank; sp_ank.mark_foreground = color_ank;
        sp_kne.mark_background = color_kne; sp_kne.mark_foreground = color_kne;
        sp_hip.mark_background = color_hip; sp_hip.mark_foreground = color_hip;
        sp_pel.mark_background = color_pel; sp_pel.mark_foreground = color_pel;
        
        p_ank_sp.mark_background = color_ank; p_ank_sp.mark_foreground = color_ank;
        p_kne_sp.mark_background = color_kne; p_kne_sp.mark_foreground = color_kne;
        p_hip_sp.mark_background = color_hip; p_hip_sp.mark_foreground = color_hip;
        p_pel_sp.mark_background = color_pel; p_pel_sp.mark_foreground = color_pel;
    end
    
    drawnow();
endfunction

function StartStop_callback(handles)
    global active_model;
    
    if active_model == 0 then
        messagebox('Please select a Machine Learning Model before playing the simulation.', 'No Model Selected', 'error');
        set(handles.StartStop, 'Value', 0); 
        return; 
    end
    
    is_pressed = get(handles.StartStop, 'Value');
    
    if is_pressed == 1 then
        set(handles.StartStop, 'String', 'STOP');
        set(handles.StartStop, 'ForegroundColor', [1, 0, 0]); 
        val = get(handles.TimeSeries, 'Value');
        
        while get(handles.StartStop, 'Value') == 1 & val < 1.0
            val = val + 0.01; 
            set(handles.TimeSeries, 'Value', val); 
            TimeSeries_callback(handles); 
            sleep(20); 
        end
        
        if val >= 1.0 then
            set(handles.TimeSeries, 'Value', 0);
            set(handles.StartStop, 'Value', 0); 
            set(handles.StartStop, 'String', 'PLAY');
            set(handles.StartStop, 'ForegroundColor', [0, 0.2, 0.4]); 
            TimeSeries_callback(handles); 
        end
    else
        set(handles.StartStop, 'String', 'PLAY');
        set(handles.StartStop, 'ForegroundColor', [0, 0.2, 0.4]); 
    end
endfunction
