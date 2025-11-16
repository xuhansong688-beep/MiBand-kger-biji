clear; clc; close all;
% Continuous process model from the TP
Gp = tf(4.65, conv([0.6 1],[0.6 1]), 'InputDelay', 0.4);
Te = 0.1;    % sampling period

ZN时域
figure; step(Gp); grid on; title('Step Response for ZN Identification');
[y,t] = step(Gp);

% Compute slope to find inflection point
dy = diff(y)./diff(t);
[~, idx] = max(dy);     % max slope point = inflection point

% Tangent method
t0 = t(idx);
y0 = y(idx);
slope = dy(idx);

% Compute L and tau
AG0 = y(end);
L = t0 - y0/slope;
tau = (AG0 - y0)/slope;

fprintf("ZN Step Method results:\n");
fprintf("  L = %.4f sec\n", L);
fprintf("  tau = %.4f sec\n", tau);

% PI parameters (ZN time)
Kp_PI = 0.9 * tau / (4.65*L);
Ti_PI  = 3*L;

% PID parameters (ZN time)
Kp_PID = 1.2 * tau / (4.65*L);
Ti_PID = 2*L;
Td_PID = L/2;

fprintf("\nZN Time-domain tuning:\n");
fprintf(" PI:  Kp = %.3f, Ti = %.3f\n", Kp_PI, Ti_PI);
fprintf(" PID: Kp = %.3f, Ti = %.3f, Td = %.3f\n", Kp_PID, Ti_PID, Td_PID);

ZN频域

% Loop for Kc identification
Kp = 0.1;
stable = true;

while stable
    CL = feedback(Kp*Gp,1);
    poles = pole(CL);
    if max(real(poles)) > 0    % unstable
        stable=false;
        break;
    end
    Kp = Kp + 0.1;
end

Kc = Kp;
fprintf("\nFound critical gain Kc = %.3f\n", Kc);

% Critical period Tc
[y2,t2] = step(feedback(Kc*Gp,1));
[~, locs] = findpeaks(y2);
Tc = mean(diff(t2(locs)));

fprintf("Critical period Tc = %.3f sec\n", Tc);

% PI
Kp_PI_f = 0.45*Kc;
Ti_PI_f = 0.8*Tc;

% PID
Kp_PID_f = 0.6*Kc;
Ti_PID_f = 0.5*Tc;
Td_PID_f = 0.125*Tc;

fprintf("\nZN Frequency-domain tuning:\n");
fprintf(" PI:  Kp = %.3f, Ti = %.3f\n", Kp_PI_f, Ti_PI_f);
fprintf(" PID: Kp = %.3f, Ti = %.3f, Td = %.3f\n", Kp_PID_f, Ti_PID_f, Td_PID_f);

T方法
% Takahashi formulas
Kp_T = 1.2*tau /(L+Te);
Ti_T = 0.3*tau*Te/(L+Te/2)^2;
Td_T = tau /(2*Kp_T);

fprintf("\nTakahashi Digital PID parameters:\n");
fprintf(" Kp = %.4f\n Ti = %.4f\n Td = %.4f\n", Kp_T, Ti_T, Td_T);

离散化：Backward Euler
s = tf('s');
PID_c = Kp_PID * (1 + 1/(Ti_PID*s) + Td_PID*s);
PID_d = c2d(PID_c, Te, 'tustin');

fprintf("\nDiscrete PID: "); PID_d

仿真：ZOH + 数字控制器
Gd = c2d(Gp, Te, 'zoh');
CLd = feedback(PID_d * Gd, 1);

figure; step(CLd); title('Discrete closed-loop response');
grid on;

